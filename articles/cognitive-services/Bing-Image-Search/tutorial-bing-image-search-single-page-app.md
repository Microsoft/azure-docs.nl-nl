---
title: 'Zelfstudie: Een web-app van één pagina maken - Bing Afbeeldingen zoeken-API'
titleSuffix: Azure cognitive services
description: Met de Bing Afbeeldingen zoeken-API kunt u internet doorzoeken op hoogwaardige en relevante afbeeldingen. Gebruik deze zelfstudie om een webtoepassing van één pagina te bouwen waarmee zoekquery's naar de API kunnen worden verzonden. De resultaten worden dan op de webpagina weergegeven.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: bcb82ffcf4c4ad861c6523f507c816c666a71772
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351381"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Zelfstudie: Een app van één pagina maken met de Bing Afbeeldingen zoeken-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Afbeeldingen zoeken-API kunt u internet doorzoeken op hoogwaardige en relevante afbeeldingen. Gebruik deze zelfstudie om een webtoepassing van één pagina te bouwen waarmee zoekquery's naar de API kunnen worden verzonden. De resultaten worden dan op de webpagina weergegeven. Deze zelfstudie is vergelijkbaar met de [bijbehorende zelfstudie](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) voor Bing Web Search.

In de zelfstudie-app ziet u hoe u de volgende acties kunt uitvoeren:

> [!div class="checklist"]
> * Een Bing Afbeeldingen zoeken-API-aanroep uitvoeren in JavaScript
> * De zoekresultaten verbeteren aan de hand van zoekopties
> * De zoekresultaten weergeven en erdoor bladeren
> * Een API-abonnementssleutel en een Bing-client-id aanvragen en verwerken.

De volledige broncode voor deze zelfstudie is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/).
* Het framework [Express.js](https://expressjs.com/) voor Node.js. Installatie-instructies voor de broncode zijn beschikbaar in het Leesmij-bestand van het GitHub-voorbeeld.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Abonnementssleutels van gebruikers beheren en opslaan

In deze toepassing wordt gebruikgemaakt van de permanente opslag van webbrowsers om API-abonnementssleutels op te slaan. Als er geen sleutel is opgeslagen, wordt de gebruiker op de webpagina om de sleutel gevraagd. De sleutel wordt vervolgens voor later gebruik opgeslagen. Als de sleutel later door de API wordt geweigerd, verwijdert de app de sleutel uit de opslag. In dit voorbeeld wordt het globale eindpunt gebruikt. U kunt ook het eindpunt [aangepast subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.


Definieer de functies `storeValue` en `retrieveValue` voor gebruik van het `localStorage`-object (als de browser dit ondersteunt) of een cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

Met de functie `getSubscriptionKey()` wordt geprobeerd om een eerder opgeslagen sleutel op te halen met `retrieveValue`. Als er geen wordt gevonden, wordt de gebruiker om een sleutel gevraagd. Deze wordt dan opgeslagen met behulp van `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Met de HTML-`<form>`-tag `onsubmit` wordt de functie `bingWebSearch` aangeroepen om de zoekresultaten te retourneren. `bingWebSearch` gebruikt `getSubscriptionKey` om elke query te verifiëren. Zoals weergegeven in de vorige definitie, wordt de gebruiker in `getSubscriptionKey` om de sleutel gevraagd, indien de sleutel niet is ingevoerd. De sleutel wordt vervolgens opgeslagen voor verder gebruik in de toepassing.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Zoekaanvragen verzenden

In deze toepassing wordt de HTML-code `<form>` gebruikt om de zoekaanvragen van gebruikers te verzenden. Het kenmerk `onsubmit` wordt gebruikt om `newBingImageSearch()` aan te roepen.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Met de handler `onsubmit` wordt standaard `false` geretourneerd, waardoor het formulier niet wordt verzonden.

## <a name="select-search-options"></a>Zoekopties selecteren

![[formulier voor Bing Image Search]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

De Bing Afbeeldingen zoeken-API biedt verschillende [filterqueryparameters](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters) om de zoekresultaten te verfijnen en filteren. Voor het HTML-formulier in deze toepassing worden de volgende parameteropties weergegeven:

| Optie | Beschrijving |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Een vervolgkeuzelijst voor het selecteren van de markt (locatie en taal) die wordt gebruikt voor de zoekopdracht.                                                                                             |
| `query`      | Het tekstveld voor het invoeren van de zoektermen.                                                                                                                                 |
| `aspect`     | Keuzerondjes voor het kiezen van de afmetingen van de gevonden afbeeldingen: min of meer vierkant, breed of hoog.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Vervolgkeuzelijst voor het optioneel beperken van de zoekopdracht tot de meest recente dag, week of maand.                                                                                          |
| `safe`       | Een selectievakje dat aangeeft of de functie Veilig zoeken in Bing moet worden gebruikt voor het wegfilteren van resultaten voor volwassenen.                                                                                      |
| `count`      | Verborgen veld. Het aantal zoekresultaten dat moet worden geretourneerd bij elke aanvraag. Wijzig dit om minder of meer resultaten per pagina weer te geven.                                                            |
| `offset`     | Verborgen veld. De verschuiving van het eerste zoekresultaat in de aanvraag, gebruikt voor wisselgeheugengebruik. Deze waarde wordt bij een nieuwe aanvraag opnieuw ingesteld op `0`.                                                           |
| `nextoffset` | Verborgen veld. Wanneer de zoekresultaten beschikbaar zijn, wordt dit veld ingesteld op de waarde van `nextOffset` in het antwoord. Met dit veld worden overlappende resultaten op opeenvolgende pagina's voorkomen. |
| `stack`      | Verborgen veld. Een op basis van JSON versleutelde lijst met offsets voor voorgaande pagina's met zoekresultaten, zodat er terug kan worden genavigeerd naar voorgaande pagina's.                                                      |

Met de functie `bingSearchOptions()` kunnen deze opties worden omgezet in een gedeeltelijke querytekenreeks. Deze kan op zijn beurt worden gebruikt in de API-aanvragen van de app.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>De aanvraag uitvoeren

Als gebruik wordt gemaakt van de zoekquery, de optietekenreeks en de API-sleutel, gebruikt de functie `BingImageSearch()` een XMLHttpRequest-object om een aanvraag in te dienen bij het eindpunt van Bing Image Search.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Als de HTTP-aanvraag juist is voltooid, wordt met JavaScript de gebeurtenis-handler voor laden van `handleBingResponse()` aangeroepen om een geslaagde HTTP GET-aanvraag te verwerken.

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Geslaagde HTTP-aanvragen kunnen informatie over mislukte zoekacties bevatten. Als er een fout optreedt in de zoekbewerking, wordt met de Bing Afbeeldingen zoeken-API een niet-200-HTTP-statuscode geretourneerd en bevat het JSON-antwoord de foutgegevens. Daarnaast wordt met de API een leeg antwoord geretourneerd als er beperkingen gelden voor de aanvraag.

## <a name="display-the-search-results"></a>Zoekresultaten weergeven

Zoekresultaten worden weergegeven door de functie `renderSearchResults()`. Hierbij wordt de JSON gebruikt die is geretourneerd door de Bing Image Search-service en wordt de juiste weergavefunctie aangeroepen voor mogelijk geretourneerde afbeeldingen en gerelateerde zoekopdrachten.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

De zoekresultaten voor afbeeldingen komen in het JSON-antwoord te staan in het `value`-object op het hoogste niveau. Deze worden vervolgens doorgegeven aan `renderImageResults()`, waar de resultaten worden herhaald en alle items worden omgezet in HTML-code.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Met de Bing Afbeeldingen zoeken-API kunnen vier typen zoeksuggesties worden geretourneerd om de zoekervaring van de gebruikers te sturen - elk met een eigen object op het hoogste niveau:

| Suggestie         | Beschrijving                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Query's die een beschrijvend woord in de oorspronkelijke zoekopdracht vervangen door een ander beschrijvend woord. Als u bijvoorbeeld zoekt naar ‘rode bloemen', is ‘rood' een beschrijvend woord, en is ‘gele bloemen' een mogelijke suggestie. |
| `queryExpansions`  | Query's die de oorspronkelijke zoekopdracht verfijnen door meer zoektermen toe te voegen. Als u bijvoorbeeld zoekt naar ‘Microsoft Surface', is ‘Microsoft Surface Pro' een mogelijke uitbreiding van de query.                                   |
| `relatedSearches`  | Query's die ook zijn ingevoerd door andere gebruikers die de oorspronkelijke zoekopdracht hebben ingevoerd. Als u bijvoorbeeld zoekt naar ‘Mount Rainier', is ‘Mt. Saint Helens' een gerelateerde zoekopdracht.                       |
| `similarTerms`     | Query's die qua betekenis vergelijkbaar zijn met de oorspronkelijke zoekopdracht. Als u bijvoorbeeld zoekt naar 'kittens', is 'schattig' een vergelijkbare term.                                                                   |

Met deze toepassing worden alleen de `relatedItems`-suggesties weergegeven. De bijbehorende koppelingen worden in de zijbalk van de pagina geplaatst.

## <a name="rendering-search-results"></a>Weergave van zoekresultaten

In deze toepassing bevat het object `searchItemRenderers` weergavefuncties waarmee HTML wordt weergegeven voor elk soort zoekresultaat.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

In de weergavefuncties worden de volgende parameters geaccepteerd:

| Parameter         | Beschrijving                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | Het JavaScript-object met de eigenschappen van het item, zoals de bijbehorende URL en beschrijving. |
| `index` | De index van het resultaatitem binnen de bijbehorende verzameling.                                          |
| `count` | Het aantal items in de verzameling van het zoekresultaatitem.                                  |

De parameters `index` en `count` worden gebruikt om resultaten te nummeren, HTML voor collecties te genereren en inhoud te ordenen. Met name gebeurt het volgende:

* De grootte van de miniatuur van de afbeelding berekenen (de breedte varieert, met een minimum van 120 pixels, terwijl de hoogte altijd 90 pixels bedraagt).
* De HTML-`<img>`-tag wordt gebouwd om de miniatuur weer te geven.
* De HTML-`<a>`-tags worden gebouwd die zijn gekoppeld aan de afbeelding en aan de pagina die de afbeelding bevat.
* De beschrijving met informatie over de afbeelding en de bijbehorende site wordt samengesteld.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

De `height` en `width` van de miniatuurafbeelding worden gebruikt in de tag `<img>` en de velden `h` en `w` in de URL van de miniatuur. Hierdoor kan Bing een [miniatuur](../bing-web-search/resize-and-crop-thumbnails.md) van precies die grootte retourneren.

## <a name="persisting-client-id"></a>Permanente client-id

Antwoorden van de Bing Zoeken-API’s kunnen een `X-MSEdge-ClientID`-header omvatten die bij volgende aanvragen moet worden teruggestuurd naar de API. Als er meerdere Bing Zoeken-API’s worden gebruikt, moet voor al deze API’s, indien mogelijk, dezelfde client-id worden gebruikt.

Door de `X-MSEdge-ClientID`-header op te geven kunnen met Bing-API's alle zoekopdrachten van een gebruiker worden gekoppeld. Dit is handig, want

Ten eerste kan met de Bing-zoekmachine vroegere context worden toegepast op zoekopdrachten om beter kloppende resultaten te vinden voor de gebruiker. Als een gebruiker bijvoorbeeld eerder heeft gezocht naar termen die zijn gerelateerd aan zeilen, kan bij een latere zoekopdracht naar ‘knopen' de voorkeur worden gegeven aan informatie over knopen die worden gebruikt bij zeilen.

Ten tweede kunnen in Bing willekeurig gebruikers worden geselecteerd om nieuwe functies te proberen voordat deze algemeen beschikbaar worden. Door bij elke aanvraag dezelfde client-id op te geven, zien gebruikers die de functie zien, deze altijd. Zonder de client-id kan het gebeuren dat de gebruiker een functie, schijnbaar willekeurig, ziet verschijnen en verdwijnen in de zoekresultaten.

Beveiligingsbeleid voor browsers (CORS) kan ervoor zorgen dat de `X-MSEdge-ClientID`-header niet beschikbaar is in JavaScript. Deze beperking treedt op wanneer het antwoord op een zoekopdracht een andere oorsprong heeft dan de pagina waarop de zoekopdracht is uitgevoerd. In een productieomgeving kunt u dit beleid omzeilen door een serverscript te hosten waarmee de API wordt aangeroepen in hetzelfde domein als de webpagina. Omdat het script dezelfde oorsprong heeft als de webpagina, is de `X-MSEdge-ClientID`-header vervolgens beschikbaar voor JavaScript.

> [!NOTE]
> In een webtoepassing die bedoeld is voor productie, moet u de aanvraag toch aan de serverzijde uitvoeren. Anders moet de sleutel voor de Bing Search-API worden opgenomen op de webpagina, waar deze beschikbaar is voor iedereen die de bron weergeeft. Al uw gebruik van de API-abonnementssleutel wordt in rekening gebracht, zelfs aanvragen die zijn gedaan door partijen die niet zijn gemachtigd. Het is daarom van groot belang dat u uw sleutel niet algemeen beschikbaar maakt.

Voor ontwikkelingsdoeleinden kunt u de aanvraag van de Bing Web Search-API via een CORS-proxy doen. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers`-header waardoor antwoordheaders worden opgenomen in de acceptatielist en beschikbaar gemaakt voor JavaScript.

U kunt eenvoudig een CORS-proxy installeren zodat de zelfstudie-app toegang krijgt tot de client-id-header. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht uit in een opdrachtvenster:

```console
npm install -g cors-proxy-server
```

Wijzig vervolgens het Bing Web Search-eindpunt in het HTML-bestand in :\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Start ten slotte de CORS-proxy met de volgende opdracht:

```console
cors-proxy-server
```

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Afbeeldingsdetails extraheren met de Bing Afbeeldingen zoeken-API](tutorial-image-post.md)

## <a name="see-also"></a>Zie ook

* [Naslag voor Bing Afbeeldingen zoeken-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
---
title: 'Zelfstudie: Een Bing Video Search-app met één pagina bouwen'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie wordt uitgelegd hoe u de Bing Video Search-API kunt gebruiken in een webtoepassing met één pagina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 76d7f4e31934480be57cd5ac133c1c7e6a9dd364
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349602"
---
# <a name="tutorial-single-page-video-search-app"></a>Zelfstudie: Video's zoeken-app met één pagina

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.
Met de Bing Video Search-API kunt u op internet zoeken en videoresultaten ophalen die relevant zijn voor een zoekquery. In deze zelfstudie bouwen we een webtoepassing met één pagina die gebruikmaakt van de Bing Search-API om resultaten op de pagina weer te geven. De toepassing omvat HTML-, CSS- en JavaScript-onderdelen.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> Als u klikt op de JSON- en HTTP-headers onderaan de pagina, worden het JSON-antwoord en de gegevens voor de HTTP-aanvraag weergegeven. Deze gegevens kunnen handig zijn bij het verkennen van de service.

![Onbewerkte resultaten voor JSON en HTTP](./media/json-http-raw-results.png)

In deze zelfstudie-app ziet u hoe u de volgende acties kunt uitvoeren:
> [!div class="checklist"]
> * Een Bing Video Search-API-aanroep uitvoeren in JavaScript
> * Zoekopties doorgeven aan de Bing Search-API
> * Zoekresultaten voor video's weergeven of optioneel webpagina's, nieuws of afbeeldingen opnemen
> * Zoeken in een tijdsbestek van 24 uur, in de afgelopen week of maand, of in alle beschikbare tijden.
> * Door de zoekresultaten bladeren
> * De Bing-client-id en abonnementsleutel voor de API verwerken
> * Eventuele fouten verwerken

De zelfstudiepagina staat volledig op zichzelf. Er worden geen externe frameworks, opmaakmodellen of afbeeldingsbestanden gebruikt. Er wordt alleen gebruikgemaakt van ondersteunde taalfuncties voor JavaScript en het werkt met actuele versies van alle bekende webbrowsers.

In deze zelfstudie worden geselecteerde gedeelten van de broncode besproken. De volledige [broncode]() is beschikbaar. Als u het voorbeeld wilt uitvoeren, kopieert en plakt u de broncode in een teksteditor en slaat u deze op als `bing.html`.

## <a name="app-components"></a>App-onderdelen
Net zoals andere web-apps met één pagina bestaat de toepassing in deze zelfstudie uit drie onderdelen:

> [!div class="checklist"]
> * HTML: definieert de structuur en inhoud van de pagina
> * CSS: definieert het uiterlijk van de pagina
> * JavaScript: definieert het gedrag van de pagina

De meeste HTML en CSS is gangbaar en wordt daarom niet besproken in de zelfstudie. De HTML bevat het zoekformulier waarin de gebruiker een query invoert en zoekopties kiest. Het formulier is gekoppeld aan JavaScript, waarmee de zoekopdracht wordt uitgevoerd met behulp van het kenmerk `onsubmit` van de tag `<form>`:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
Met de handler `onsubmit` wordt `false` geretourneerd, waardoor het formulier niet wordt verzonden naar de server. Met de JavaScript-code worden de benodigde gegevens uit het formulier verzameld en wordt de zoekopdracht uitgevoerd.

De HTML bevat ook de delen (HTML-`<div>`-tags) waar de zoekresultaten worden weergegeven.

## <a name="managing-subscription-key"></a>Abonnementssleutel beheren

De abonnementssleutel voor de Bing Search-API wordt opgeslagen in de permanente opslag van de browser om te voorkomen dat deze moet worden opgenomen in de code. Voordat de sleutel wordt opgeslagen, vragen we om de sleutel van de gebruiker. Als de sleutel later wordt geweigerd in de API, wordt de opgeslagen sleutel ongeldig en wordt de gebruiker opnieuw om een sleutel gevraagd.

De functies `storeValue` en `retrieveValue` worden gedefinieerd. Deze maken gebruik van het `localStorage`-object (dat niet in alle browsers wordt ondersteund) of van een cookie. De functie `getSubscriptionKey()` gebruikt deze functies om de sleutel van de gebruiker op te slaan en op te halen.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
Met de HTML-`<form>`-tag `onsubmit` wordt de functie `bingWebSearch` aangeroepen om de zoekresultaten te retourneren. `bingWebSearch` gebruikt `getSubscriptionKey()` om elke query te verifiëren. Zoals weergegeven in de vorige definitie, wordt de gebruiker in `getSubscriptionKey` om de sleutel gevraagd, indien de sleutel niet is ingevoerd. De sleutel wordt vervolgens opgeslagen voor verder gebruik in de toepassing.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Zoekopties selecteren
In de volgende afbeelding ziet u het querytekstvak en de queryopties die een zoekopdracht definiëren.

![Bing News Search-opties](media/video-search-options.png)

Het HTML-formulier bevat elementen met de volgende namen:

|Element|Beschrijving|
|-|-|
| `where` | Een vervolgkeuzelijst voor het selecteren van de markt (locatie en taal) die wordt gebruikt voor de zoekopdracht. |
| `query` | Het tekstveld voor het invoeren van de zoektermen. |
| `modules` | Selectievakjes voor het promoten van bepaalde modules met resultaten, alle resultaten of gerelateerde video's. |
| `when` | Vervolgkeuzelijst voor het optioneel beperken van de zoekopdracht tot de meest recente dag, week of maand. |
| `safe` | Een selectievakje dat aangeeft of de functie Bing SafeSearch moet worden gebruikt voor het uitfilteren van resultaten voor volwassenen. |
| `count` | Verborgen veld. Het aantal zoekresultaten dat moet worden geretourneerd bij elke aanvraag. Wijzig dit om minder of meer resultaten per pagina weer te geven. |
| `offset`|  Verborgen veld. De verschuiving van het eerste zoekresultaat in de aanvraag, gebruikt voor wisselgeheugengebruik. Deze waarde wordt bij een nieuwe aanvraag opnieuw ingesteld op `0`. |

> [!NOTE]
> Bing Web Search biedt andere queryparameters. We gebruiken hier slechts een aantal van.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

De parameter `SafeSearch` in een werkelijke API-aanroep kan bijvoorbeeld `strict` of `moderate` zijn, waarbij `moderate` de standaardwaarde is.

## <a name="performing-the-request"></a>De aanvraag uitvoeren
Nadat de query, de tekenreeks met opties en de API-sleutel zijn opgegeven, gebruikt de functie `BingWebSearch` een `XMLHttpRequest`-object om een aanvraag in te dienen bij het Bing Search-eindpunt. U kunt het volgende globale eindpunt gebruiken of het eindpunt [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw resource.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
Als de HTTP-aanvraag juist is voltooid, wordt met JavaScript de gebeurtenis-handler van `load` (`handleOnLoad()`) aangeroepen om een geslaagde HTTP GET-aanvraag naar de API te verwerken. 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> Als er een fout optreedt in de zoekbewerking, wordt met de Bing News Search-API een niet-200-HTTP-statuscode geretourneerd en bevat het JSON-antwoord de foutgegevens. Daarnaast wordt met de API een leeg antwoord geretourneerd als er beperkingen gelden voor de aanvraag.
Een juist voltooide HTTP-aanvraag betekent *niet* noodzakelijkerwijs dat de zoekopdracht zelf ook is geslaagd. 

Een groot deel van de code in beide voorgaande functies is toegewezen aan foutafhandeling. Er kunnen fouten optreden in de volgende fasen:

|Fase|Potentiële fout(en)|Verwerkt met|
|-|-|-|
|Het JavaScript-aanvraagobject bouwen|Ongeldige URL|`try`/`catch`-blok|
|De aanvraag indienen|Netwerkfouten, afgebroken verbindingen|Gebeurtenis-handlers `error` en `abort`|
|De zoekopdracht uitvoeren|Ongeldige aanvraag, ongeldige JSON, geldende beperkingen|Tests in gebeurtenis-handler van `load`|

Fouten worden afgehandeld door `renderErrorMessage()` aan te roepen met eventuele bekende details over de fout. Als het antwoord de volledige set met fouttests doorgeeft, wordt `renderSearchResults()` aangeroepen om de zoekresultaten op de pagina weer te geven.

## <a name="displaying-search-results"></a>Zoekresultaten weergeven
De hoofdfunctie voor het weergeven van de zoekresultaten is `renderSearchResults()`. Deze functie maakt gebruik van de JSON die is geretourneerd met de Bing News Search-service, en zorgt ervoor dat de nieuwsresultaten en gerelateerde zoekopdrachten worden weergegeven, indien van toepassing.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
De zoekresultaten worden geretourneerd als `value`-object op het hoogste niveau in het JSON-antwoord. Deze worden doorgegeven aan de functie `renderResultsItems()`. Hier worden ze verwerkt en wordt een functie aangeroepen om elk item weer te geven in HTML. De resulterende HTML wordt geretourneerd naar `renderSearchResults()`, waar deze wordt ingevoegd in het deel `results` op de pagina.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

Met de Bing News Search-API worden maximaal vier verschillende soorten gerelateerde resultaten geretourneerd, elk in een eigen object op het hoogste niveau. Dit zijn:

|Relatie|Beschrijving|
|-|-|
|`pivotSuggestions`|Query's die een beschrijvend woord in de oorspronkelijke zoekopdracht vervangen door een ander beschrijvend woord. Als u bijvoorbeeld zoekt naar ‘rode bloemen', is ‘rood' een beschrijvend woord, en is ‘gele bloemen' een mogelijke suggestie.|
|`queryExpansions`|Query's die de oorspronkelijke zoekopdracht verfijnen door meer zoektermen toe te voegen. Als u bijvoorbeeld zoekt naar ‘Microsoft Surface', is ‘Microsoft Surface Pro' een mogelijke uitbreiding van de query.|
|`relatedSearches`|Query's die ook zijn ingevoerd door andere gebruikers die de oorspronkelijke zoekopdracht hebben ingevoerd. Als u bijvoorbeeld zoekt naar ‘Mount Rainier', is ‘Mt. Saint Helens' een gerelateerde zoekopdracht.|
|`similarTerms`|Query's die qua betekenis vergelijkbaar zijn met de oorspronkelijke zoekopdracht. Als u bijvoorbeeld zoekt naar ‘scholen', is ‘onderwijs' een vergelijkbare term.|

Zoals eerder aangetoond in `renderSearchResults()` worden alleen de `relatedItems`-suggesties weergegeven en zijn de resulterende koppelingen zichtbaar in de zijbalk van de pagina.

## <a name="rendering-result-items"></a>Resultaten weergeven

In de JavaScript-code kan het object (`searchItemRenderers`) *renderers* bevatten: functies waarmee HTML wordt weergegeven voor elk soort zoekresultaat. Op de pagina voor het zoeken van video's wordt alleen `videos` gebruikt. Zie andere zelfstudies voor verschillende typen renderers.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Met een renderer kunnen de volgende parameters worden geaccepteerd:

|Parameter|Beschrijving|
|-|-|
|`item`| Het JavaScript-object met de eigenschappen van het item, zoals de bijbehorende URL en beschrijving.|
|`index`| De index van het resultaatitem binnen de bijbehorende verzameling.|
|`count`| Het aantal items in de verzameling van het zoekresultaatitem.|

De parameters `index` en `count` kunnen worden gebruikt om resultaten te nummeren, speciale HTML te genereren voor het begin en einde van een verzameling, regeleinden in te voegen na een bepaald aantal items, enzovoort. Als een renderer deze functionaliteit niet nodig heeft, hoeven deze twee parameters niet te worden geaccepteerd.

De `video`-renderer wordt weergegeven zoals in het volgende JavaScript-fragment. Als het eindpunt Video's wordt gebruikt, zijn alle resultaten van het type `Videos`. De `searchItemRenderers` worden weergegeven in het volgende codesegment.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

Met de renderer worden de volgende handelingen uitgevoerd:
> [!div class="checklist"]
> * Er wordt een alineatag gemaakt. Deze wordt toegewezen aan de klasse `images` en naar de HTML-matrix gepusht.
> * De afbeeldingsgrootte van de miniatuur wordt berekend (breedte is vastgesteld op 60 pixels, hoogte wordt naar verhouding berekend).
> * De HTML-`<img>`-tag wordt gebouwd om de miniatuur weer te geven. 
> * De HTML-`<a>`-tags worden gebouwd die zijn gekoppeld aan de afbeelding en aan de pagina die de afbeelding bevat.
> * De beschrijving met informatie over de afbeelding en de bijbehorende site wordt samengesteld.

De miniatuurgrootte wordt gebruik in de `<img>`-tag en in de velden `h` en `w` in de URL van de miniatuur. Bing retourneert een [miniatuur](../bing-web-search/resize-and-crop-thumbnails.md) van precies die grootte.

## <a name="persisting-client-id"></a>Permanente client-id
Antwoorden van de Bing Search-API kunnen een `X-MSEdge-ClientID`-header omvatten die bij volgende aanvragen moet worden teruggestuurd naar de API. Als er meerdere Bing Search-API's worden gebruikt, moet voor al deze API's, indien mogelijk, dezelfde client-id worden gebruikt.

Door de `X-MSEdge-ClientID`-header op te geven kunnen met Bing-API's alle zoekopdrachten van een gebruiker worden gekoppeld. Dit heeft twee belangrijke voordelen.

Ten eerste kan met de Bing-zoekmachine vroegere context worden toegepast op zoekopdrachten om beter kloppende resultaten te vinden voor de gebruiker. Als een gebruiker bijvoorbeeld eerder heeft gezocht naar termen die zijn gerelateerd aan zeilen, kan bij een latere zoekopdracht naar ‘knopen' de voorkeur worden gegeven aan informatie over knopen die worden gebruikt bij zeilen.

Ten tweede kunnen in Bing willekeurig gebruikers worden geselecteerd om nieuwe functies uit te proberen voordat deze algemeen beschikbaar worden. Door bij elke aanvraag dezelfde client-id op te geven zien gebruikers die de functie zien, deze altijd. Zonder de client-id kan het gebeuren dat de gebruiker een functie, schijnbaar willekeurig, ziet verschijnen en verdwijnen in de zoekresultaten.

Beveiligingsbeleid voor browsers (CORS) kan ervoor zorgen dat de `X-MSEdge-ClientID`-header niet beschikbaar is in JavaScript. Deze beperking treedt op wanneer het antwoord op een zoekopdracht een andere oorsprong heeft dan de pagina waarop de zoekopdracht is uitgevoerd. In een productieomgeving kunt u dit beleid omzeilen door een serverscript te hosten waarmee de API wordt aangeroepen in hetzelfde domein als de webpagina. Omdat het script dezelfde oorsprong heeft als de webpagina, is de `X-MSEdge-ClientID`-header vervolgens beschikbaar voor JavaScript.

> [!NOTE]
> In een webtoepassing die bedoeld is voor productie, moet u de aanvraag aan de serverzijde uitvoeren. Anders moet de sleutel voor de Bing Search-API worden opgenomen op de webpagina, waar deze beschikbaar is voor iedereen die de bron weergeeft. Al uw gebruik van de API-abonnementssleutel wordt in rekening gebracht, zelfs aanvragen die zijn gedaan door partijen die niet zijn gemachtigd. Het is daarom van groot belang dat u uw sleutel niet algemeen beschikbaar maakt.

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
> [Referentie voor de Bing Video Search-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)
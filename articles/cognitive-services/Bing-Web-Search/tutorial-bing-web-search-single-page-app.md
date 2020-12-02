---
title: 'Zelfstudie: Een web-app van één pagina maken - Bing Webzoekopdrachten-API'
titleSuffix: Azure Cognitive Services
description: Deze app met één pagina laat zien aan hoe de Bing Webzoekopdrachten-API kan worden gebruikt voor het ophalen, parseren en weergeven van relevante zoekresultaten in een app met één pagina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ff54d605fd81fa640314d99359f1aabacf7a469e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350378"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Zelfstudie: Een app van één pagina maken met de Bing Webzoekopdrachten-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Deze app met één pagina laat zien hoe u zoekresultaten uit de Bing Webzoekopdrachten-API ophaalt, parseert en weergeeft. De zelfstudie gebruikt standaard HTML en CSS en is gericht op de JavaScript-code. Op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) zijn HTML-, CSS- en JS-bestanden met snelstartinstructies beschikbaar.

Met deze voorbeeld-app kunt u:

> [!div class="checklist"]
> * De Bing Webzoekopdrachten-API met zoekopties aanroepen
> * Web-, afbeeldings-, nieuws- en videoresultaten weergeven
> * Paginaresultaten
> * Abonnementssleutels beheren
> * Fouten verwerken

Voor het gebruik van deze app is een [Azure Cognitive Services-account](../cognitive-services-apis-create-account.md) met Bing Zoeken-API's vereist.

## <a name="prerequisites"></a>Vereisten

Hier zijn een aantal zaken die u nodig hebt om de app uit te voeren:

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Een Bing Search-resource maken"  target="_blank">maakt u een Bing Search-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.

* Node.js 8 of later

De eerste stap bestaat uit het klonen van de opslagplaats met de broncode van de voorbeeld-app.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Voer vervolgens `npm install` uit. Voor deze zelfstudie is Express.js de enige afhankelijkheid.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>App-onderdelen

De voorbeeld-app die we gaan bouwen, bestaat uit vier onderdelen:

* `bing-web-search.js`: onze Express.js-app. Hiermee verwerkt u de aanvraag-/antwoordlogica en routering.
* `public/index.html`: de basis van onze app; hiermee wordt gedefinieerd hoe gegevens aan de gebruiker worden aangeboden.
* `public/css/styles.css`: hiermee worden paginastijlen zoals lettertypen, kleuren en tekengrootte gedefinieerd.
* `public/js/scripts.js`: bevat de logica om aanvragen naar de Bing Webzoekopdrachten-API te maken, abonnementssleutels te beheren, antwoorden te verwerken en te parseren en resultaten weer te geven.

Deze zelfstudie is gericht op `scripts.js` en de logica die is vereist om de Bing Webzoekopdrachten-API aan te roepen en het antwoord te verwerken.

## <a name="html-form"></a>HTML-formulier

`index.html` bevat een formulier waarmee gebruikers kunnen zoeken en zoekopties kunnen selecteren. Het kenmerk `onsubmit` wordt geactiveerd wanneer het formulier is ingediend, waardoor de methode `bingWebSearch()` die in `scripts.js` is gedefinieerd, wordt aangeroepen. Hiervoor zijn drie argumenten nodig:

* Zoekquery
* Geselecteerde opties
* Abonnementssleutel

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Queryopties

Het HTML-formulier bevat opties waarmee queryparameters worden toegewezen in de [Bing Webzoekopdrachten-API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters). Deze tabel bevat een overzicht van de manier waarop gebruikers zoekresultaten kunnen filteren met behulp van de voorbeeld-app:

| Parameter | Beschrijving |
|-----------|-------------|
| `query` | Een tekstveld voor het invoeren van een querytekenreeks. |
| `where` | Een vervolgkeuzelijst om de markt te selecteren (locatie en taal). |
| `what` | Selectievakjes voor het promoten van specifieke resultaattypen. Door bijvoorbeeld afbeeldingen te promoten, verhoogt u de beoordeling van afbeeldingen in zoekresultaten. |
| `when` | Een vervolgkeuzelijst waarmee de gebruiker de zoekresultaten kan beperken tot vandaag, deze week of deze maand. |
| `safe` | Een selectievakje voor het inschakelen van Bing SafeSearch, waarmee inhoud voor volwassenen wordt weggefilterd. |
| `count` | Verborgen veld. Het aantal zoekresultaten dat moet worden geretourneerd bij elke aanvraag. Wijzig deze waarde om minder of meer resultaten per pagina weer te geven. |
| `offset` | Verborgen veld. De verschuiving van het eerste zoekresultaat in de aanvraag. Dit wordt gebruikt voor wisselgeheugengebruik. Deze waarde wordt bij elke nieuwe aanvraag opnieuw ingesteld op `0`. |

> [!NOTE]
> De Bing Webzoekopdrachten-API biedt aanvullende queryparameters om de zoekresultaten te verfijnen. In dit voorbeeld worden er maar een paar gebruikt. Zie de [Bing Webzoekopdrachten-API v7-naslag](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) voor een complete lijst met beschikbare parameters.

Met de functie `bingSearchOptions()` converteert u deze opties zodat ze overeenkomen met de indeling die voor de Bing Zoeken-API is vereist.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`SafeSearch` kan worden ingesteld op `strict`, `moderate` of `off`, waarbij `moderate` de standaardinstelling voor Bing Web Search is. In dit formulier wordt een selectievakje met twee statussen gebruikt: `strict` of `moderate`.

Als een van de **Promoten**-selectievakjes is geselecteerd, wordt de `answerCount`-parameter toegevoegd aan de query. `answerCount` is vereist wanneer u de `promote`-parameter gebruikt. In dit fragment is de waarde ingesteld op `9` zodat alle beschikbare resultaattypen worden geretourneerd.
> [!NOTE]
> Het promoten van een resultaattype kan *niet garanderen* dat dit type in de zoekresultaten komt te staan. In plaats daarvan verhoogt u met promotie de beoordeling van dat soort resultaten in vergelijking met hun normale beoordeling. Als u de zoekopdrachten wilt beperken tot een bepaald type resultaten, gebruikt u de `responseFilter`-queryparameter of roept een specifieker eindpunt aan zoals Bing Image Search of Bing News Search.

De queryparameters `textDecoration` en `textFormat` zijn in code opgenomen in het script en zorgen ervoor dat de zoekterm in de zoekresultaten vetgedrukt wordt weergegeven. Deze parameters zijn niet vereist.

## <a name="manage-subscription-keys"></a>Abonnementssleutels beheren

Deze voorbeeld-app gebruikt een permanente opslag van de browser om de abonnementssleutel op te slaan om het in code opnemen van de abonnementssleutel van de Bing Zoeken-API te voorkomen. Als er geen abonnementssleutel is opgeslagen, wordt de gebruiker gevraagd een sleutel in te voeren. Als de abonnementssleutel door de API wordt geweigerd, wordt de gebruiker gevraagd opnieuw een abonnementssleutel in te voeren.

De functie `getSubscriptionKey()` gebruikt de functies `storeValue` en `retrieveValue` om de abonnementssleutel van een gebruiker op te slaan en op te halen. Deze functies gebruiken het `localStorage`-object (indien ondersteund) of cookies.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Zoals we eerder al hebben gezien, wordt `onsubmit` geactiveerd wanneer het formulier wordt ingediend, waardoor `bingWebSearch` wordt aangeroepen. Met deze functie wordt de aanvraag geïnitialiseerd en verzonden. Steeds wanneer een formulier wordt ingediend om de aanvraag te verifiëren, wordt `getSubscriptionKey` aangeroepen.

## <a name="call-bing-web-search"></a>Bing Web Search aanroepen

Nadat de query, de tekenreeks met opties en de abonnementssleutel zijn opgegeven, gebruikt de functie `BingWebSearch` een `XMLHttpRequest`-object om het Bing Webzoekopdrachten-eindpunt aan te roepen.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Na een geslaagde aanvraag wordt de `load`-gebeurtenis-handler geactiveerd en wordt de `handleBingResponse`-functie aangeroepen. `handleBingResponse` parseert het resultaatobject, toont de resultaten en bevat foutlogica voor mislukte aanvragen.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Een juist voltooide HTTP-aanvraag betekent *niet* dat de zoekopdracht zelf ook is geslaagd. Als er een fout optreedt in de zoekbewerking, wordt met de Bing Webzoekopdrachten-API een niet-200-HTTP-statuscode geretourneerd en bevat het JSON-antwoord de foutgegevens. Als er beperkingen gelden voor de aanvraag wordt met de API een leeg antwoord geretourneerd.

Een groot deel van de code in beide voorgaande functies is toegewezen aan foutafhandeling. Er kunnen fouten optreden in de volgende fasen:

| Fase | Potentiële fout(en) | Verwerkt met |
|-------|--------------------|------------|
| Het aanvraagobject bouwen | Ongeldige URL | `try` / `catch`-blok |
| De aanvraag indienen | Netwerkfouten, afgebroken verbindingen | Gebeurtenis-handlers `error` en `abort` |
| De zoekopdracht uitvoeren | Ongeldige aanvraag, ongeldige JSON, geldende beperkingen | Tests in gebeurtenis-handler van `load` |

Fouten worden verwerkt door `renderErrorMessage()` aan te roepen. Als het antwoord alle fouttesten doorstaat, wordt `renderSearchResults()` aangeroepen om de zoekresultaten weer te geven.

## <a name="display-search-results"></a>Zoekresultaten weergeven

Er zijn [gebruiks- en weergavevereisten](./use-display-requirements.md) voor resultaten die door de Bing Webzoekopdrachten-API worden geretourneerd. Aangezien een antwoord diverse resultaattypen kan bevatten, is alleen het herhalen van de `WebPages`-verzameling op het hoogste niveau niet voldoende. In plaats daarvan gebruikt de voorbeeld-app `RankingResponse` om de resultaten op specificaties te sorteren.

> [!NOTE]
> Als u maar één resultaattype wilt krijgen, gebruikt u de `responseFilter`-queryparameter, of gebruik u een van de andere Bing zoeken-eindpunten, zoals Bing Image Search.

Elk antwoord heeft een `RankingResponse`-object dat maximaal drie verzamelingen kan bevatten: `pole`, `mainline` en `sidebar`. `pole` (indien beschikbaar) is het relevantste zoekresultaat en moet prominent worden weergegeven. `mainline` bevat de meeste zoekresultaten en wordt onmiddellijk na `pole` weergegeven. `sidebar` bevat aanvullende zoekresultaten. Deze resultaten moeten waar mogelijk in de zijbalk worden weergegeven. Als een zijbalk door schermbeperkingen niet haalbaar is, moeten deze resultaten na de `mainline`-resultaten worden weergegeven.

Elke `RankingResponse` bevat een `RankingItem`-array waarin wordt opgegeven hoe resultaten moeten worden geordend. Onze voorbeeld-app gebruikt de parameters `answerType` en `resultIndex` om het resultaat te identificeren.

> [!NOTE]
> Er zijn meer manieren om resultaten te identificeren en te beoordelen. Voor meer informatie raadpleegt u [Beoordeling gebruiken om resultaten weer te geven](rank-results.md).

Laten we de code eens bekijken:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

De `renderResultsItems()`-functie doorloopt de items in elke `RankingResponse`-verzameling, wijst elk beoordelingsresultaat toe aan een zoekresultaat met behulp van de waarden `answerType` en `resultIndex` en roept de toepasselijke renderingsfunctie aan om de HTML te genereren. Als `resultIndex` niet is opgegeven voor een item, doorloopt `renderResultsItems()` alle resultaten van dat type en wordt voor elk item de renderingsfunctie aangeroepen. De resulterende HTML wordt in het toepasselijke `<div>`-element in `index.html` ingevoegd.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>Rendererfuncties controleren

In onze voorbeeld-app bevat het `searchItemRenderers`-object functies die HTML genereren voor elk type zoekresultaat.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> De voorbeeld-app beschikt over renderers voor webpagina's, nieuws, afbeeldingen, video's en gerelateerde zoekopdrachten. Uw toepassing heeft renderers nodig voor elk type resultaten dat wordt ontvangen, zoals berekeningen, spellingsuggesties, entiteiten, tijdzones en definities.

Een aantal renderingsfuncties accepteert alleen de `item`-parameter. Andere functies accepteren aanvullende parameters, die kunnen worden gebruikt om items verschillend te renderen op basis van de context. Een renderer die deze informatie niet gebruikt, hoeft deze parameters niet te accepteren.

De contextargumenten zijn:

| Parameter  | Beschrijving |
|------------|-------------|
| `section` | De sectie met resultaten (`pole`, `mainline` of `sidebar`) waarin het item wordt weergegeven. |
| `index`<br>`count` | Beschikbaar wanneer het `RankingResponse`-item aangeeft dat alle resultaten in een gegeven verzameling moeten worden weergegeven; anders `undefined`. De index van het item binnen de bijbehorende verzameling en het totale aantal items in die verzameling. U kunt deze informatie gebruiken om de resultaten te nummeren, om verschillende HTML voor het eerste of laatste resultaat te genereren, etc. |

In de voorbeeld-app gebruiken zowel de `images`- als `relatedSearches`-renderers de contextargumenten om de gegenereerde HTML aan te passen. Laten we de `images`-renderer eens beter bekijken:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

De afbeeldingsrenderer:

* Hiermee wordt de grootte van de miniatuur van de afbeelding (de breedte varieert, terwijl de hoogte altijd 60 pixels bedraagt) berekend.
* Hiermee wordt de HTML ingevoegd die voorafgaat aan het afbeeldingsresultaat op basis van de context.
* Hiermee wordt de HTML-`<a>`-tag gebouwd die is gekoppeld aan de pagina die de afbeelding bevat.
* De HTML-`<img>`-tag wordt gebouwd om de miniatuur weer te geven.

De afbeeldingsrenderer gebruikt de variabelen `section` en `index` om resultaten verschillend weer te geven, afhankelijk van de locatie waar ze worden weergegeven. Tussen afbeeldingsresultaten wordt in de zijbalk een regeleinde (`<br>`-tag) ingevoegd, zodat in de zijbalk een kolom met afbeeldingen wordt weergegeven. In andere secties wordt het eerste afbeeldingsresultaat `(index === 0)` voorafgegaan door een `<p>`-tag.

De miniatuurgrootte wordt gebruik in de `<img>`-code en in de velden `h` en `w` in de URL van de miniatuur. De kenmerken `title` en `alt` (een tekstuele beschrijving van de afbeelding) zijn opgebouwd uit de naam van de afbeelding en de hostnaam in de URL.

Hier ziet u een voorbeeld van de manier waarop afbeeldingen worden weergegeven in de voorbeeld-app:

![[Resultaten Bing-afbeeldingen]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>De client-id persistent maken

Antwoorden van de Bing Zoeken-API kunnen een `X-MSEdge-ClientID`-header omvatten die bij elke volgende aanvraag moet worden teruggestuurd naar de API. Als meer dan één van de Bing Zoeken-API's door uw app wordt gebruikt, moet u ervoor zorgen dat dezelfde client-id wordt verzonden bij elke aanvraag vanuit alle services.

Door de `X-MSEdge-ClientID`-header op te geven kunnen met Bing-API's zoekopdrachten van gebruikers worden gekoppeld. Ten eerste kan met de Bing-zoekmachine vroegere context worden toegepast op zoekopdrachten om beter kloppende resultaten te vinden voor de aanvraag. Als een gebruiker bijvoorbeeld eerder heeft gezocht naar termen die zijn gerelateerd aan zeilen, kan bij een latere zoekopdracht naar ‘knopen' de voorkeur worden gegeven aan informatie over knopen die worden gebruikt bij zeilen. Ten tweede kunnen in Bing willekeurig gebruikers worden geselecteerd om nieuwe functies te proberen voordat deze algemeen beschikbaar worden. Door bij elke aanvraag dezelfde client-id op te geven, zien gebruikers die de functie zien, deze altijd. Zonder de client-id kan het gebeuren dat de gebruiker een functie, schijnbaar willekeurig, ziet verschijnen en verdwijnen in de zoekresultaten.

Met beveiligingsbeleid voor de browser, zoals Cross-Origin Resource Sharing (CORS), kan worden voorkomen dat de voorbeeld-app toegang heeft tot de `X-MSEdge-ClientID`-header. Deze beperking treedt op wanneer het antwoord op een zoekopdracht een andere oorsprong heeft dan de pagina waarop de zoekopdracht is uitgevoerd. In een productieomgeving kunt u dit beleid omzeilen door een serverscript te hosten waarmee de API wordt aangeroepen in hetzelfde domein als de webpagina. Omdat het script dezelfde oorsprong heeft als de webpagina, is de `X-MSEdge-ClientID`-header vervolgens beschikbaar voor JavaScript.

> [!NOTE]
> In een webtoepassing die bedoeld is voor productie, moet u de aanvraag toch aan de serverzijde uitvoeren. Anders moet de abonnementssleutel voor de Bing Zoeken-API worden opgenomen op de webpagina, waar deze beschikbaar is voor iedereen die de bron weergeeft. Al uw gebruik van de API-abonnementssleutel wordt in rekening gebracht, zelfs aanvragen die zijn gedaan door partijen die niet zijn gemachtigd. Het is daarom van groot belang dat u uw sleutel niet algemeen beschikbaar maakt.

Voor ontwikkelingsdoeleinden kunt u de aanvraag via een CORS-proxy doen. Het antwoord van dit type proxy heeft een `Access-Control-Expose-Headers`-header waardoor antwoordheaders worden gefilterd en beschikbaar gemaakt voor JavaScript.

U kunt eenvoudig een CORS-proxy installeren zodat de voorbeeld-app toegang krijgt tot de client-id-header. Voer deze opdracht uit:

```console
npm install -g cors-proxy-server
```

Wijzig vervolgens het Bing Web Search-eindpunt in `script.js` naar:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Start de CORS-proxy met deze opdracht:

```console
cors-proxy-server
```

Laat het opdrachtvenster geopend terwijl u de voorbeeld-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitvouwbare sectie HTTP-headers onder de zoekresultaten moet de `X-MSEdge-ClientID`-header zichtbaar zijn. Verifieer dat deze voor elke aanvraag hetzelfde is.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Web Search API v7 reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) (Naslaggids Web Search API v7)
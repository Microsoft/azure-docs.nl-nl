---
title: 'Zelfstudie: Web-app met één pagina maken met Bing Entiteiten zoeken'
titleSuffix: Azure Cognitive Services
description: Deze zelfstudie leert u hoe u de Bing Entiteiten zoeken-API kunt gebruiken in een webtoepassing met één pagina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: f2c15221268635ca1892a9292d5b0c208c13dd34
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426804"
---
# <a name="tutorial-single-page-web-app"></a>Zelfstudie: Web-app van één pagina

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Entiteiten zoeken-API kunt u op internet zoeken naar informatie over *entiteiten* en *plaatsen*. U kunt in een query een van beide soorten resultaten opvragen of beide. Hieronder vindt u de definities van plaatsen en entiteiten.

| Resultaat | Beschrijving |
|-|-|
|Entiteiten|Bekende personen, plaatsen en dingen die u op naam zoekt|
|Plaatsen|Restaurants, hotels en andere lokale bedrijven die u op naam zoekt *of* op type (Italiaanse restaurants)|

In deze zelfstudie bouwen we een webtoepassing met één pagina die gebruikmaakt van de Bing Entiteiten zoeken-API om resultaten direct op de pagina weer te geven. De toepassing omvat HTML-, CSS- en JavaScript-onderdelen.

De API biedt de mogelijkheid om resultaten op basis van locatie prioriteit te geven. In een mobiele app kunt u het apparaat om de eigen locatie vragen. In een web-app kunt u de functie `getPosition()` gebruiken. Maar deze aanroep werkt alleen in veilige contexten, en kan mogelijk geen nauwkeurige locatie geven. Misschien wil de gebruiker ook zoeken naar entiteiten in de buurt van een andere locatie dan hun eigen locatie.

Onze app roept daarom de Bing Kaarten-service aan om de breedtegraad en lengtegraad op te halen van een door de gebruiker ingevoerde locatie. De gebruiker kan vervolgens de naam van een oriëntatiepunt ('Space Needle') of een volledig of gedeeltelijk adres ('New York City') opgeven en de Bing Kaarten-API levert dan de coördinaten.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Als u klikt op de JSON- en HTTP-koppen onder aan de pagina worden het JSON-antwoord en de gegevens voor de HTTP-aanvraag weergegeven. Deze gegevens zijn handig zijn bij het verkennen van de service.

In de zelfstudie-app leert u het volgende:

> [!div class="checklist"]
> * Een aanroep versturen naar de Bing Entiteiten zoeken-API in JavaScript
> * Een aanroep naar de Bing Kaarten `locationQuery`-API in JavaScript versturen
> * Zoekopties doorgeven aan de API-aanroepen
> * Zoekresultaten weergeven
> * De Bing-client-id en API-abonnementsleutels verwerken
> * Eventuele fouten verwerken

De zelfstudiepagina staat volledig op zichzelf. Er worden geen externe frameworks, opmaakmodellen of afbeeldingsbestanden gebruikt. Er wordt alleen gebruikgemaakt van ondersteunde taalfuncties voor JavaScript en het werkt met actuele versies van alle bekende webbrowsers.

In deze zelfstudie bespreken we alleen bepaalde gedeelten van de broncode. De volledige broncode is beschikbaar [op een afzonderlijke pagina](). Kopieer en plak deze code in een teksteditor en sla deze op als `bing.html`.

> [!NOTE]
> Deze zelfstudie is in grote lijnen vergelijkbaar met de [zelfstudie over het maken van een app met één pagina met de Bing Webzoekopdrachten-API](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), alleen worden hier alleen de zoekresultaten voor entiteiten behandeld.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt volgen, hebt u een abonnementssleutel nodig voor de Bing Zoeken-API en Bing Kaarten-API. 

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u uw Azure-abonnement heeft geselecteerd:
  * <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Een Bing Zoeken-resource maken"  target="_blank">Een Bing Zoeken-resource maken </a> in de Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
  * <a href="https://www.microsoft.com/maps/create-a-bing-maps-key.aspx"  title="Een Computer Vision-resource maken"  target="_blank">Een Bing Kaarten-resource maken </a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.

## <a name="app-components"></a>App-onderdelen

Net zoals elke andere web-app met één pagina, bestaat de toepassing in deze zelfstudie uit drie onderdelen:

> [!div class="checklist"]
> * HTML: definieert de structuur en inhoud van de pagina
> * CSS: definieert het uiterlijk van de pagina
> * JavaScript: definieert het gedrag van de pagina

In deze zelfstudie wordt niet uitvoerig ingegaan op het merendeel van de HTML of CSS, aangezien deze heel duidelijk zijn.

De HTML bevat het zoekformulier waarin de gebruiker een query invoert en zoekopties kiest. Het formulier is via het kenmerk `onsubmit` van de tag `<form>` van het formulier verbonden met de JavaScript:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

Met de handler `onsubmit` wordt `false` geretourneerd, waardoor het formulier niet wordt verzonden naar de server. De JavaScript-code zorgt voor het verzamelen van de benodigde gegevens uit het formulier en het vervolgens uitvoeren van de zoekopdracht.

De zoekopdracht wordt in twee fasen uitgevoerd. Als de gebruiker heeft een locatiebeperking heeft ingevoerd, wordt er eerst een Bing Kaarten-query uitgevoerd om de locatie om te zetten in coördinaten. De callback voor deze query activeert vervolgens de Bing Entiteiten zoeken-query.

De HTML bevat ook de delen (HTML-`<div>`-tags) waar de zoekresultaten worden weergegeven.

## <a name="managing-subscription-keys"></a>Abonnementssleutels beheren

> [!NOTE]
> Deze app vereist abonnementssleutels voor zowel de Bing zoeken-API als de Bing Kaarten-API.

Om te voorkomen dat de abonnementssleutels van de Bing Zoeken- en Bing Kaarten-API's moeten worden opgenomen in de code, gebruiken we de permanente opslag van de browser om de sleutels op te slaan. Als een van beide sleutels niet is opgeslagen, wordt de sleutel opgevraagd en opgeslagen voor later gebruik. Als de sleutel later wordt geweigerd door de API, wordt de opgeslagen sleutel ongeldig gemaakt zodat de gebruiker bij de volgende zoekopdracht om een sleutel wordt gevraagd.

We definiëren de functies `storeValue` en `retrieveValue` voor gebruik met het `localStorage`-object (als de browser dit ondersteunt) of een cookie. Onze functie `getSubscriptionKey()` gebruikt deze functies voor het opslaan en ophalen van de sleutel van de gebruiker. U kunt het volgende globale eindpunt gebruiken of het eindpunt [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) dat wordt weergegeven in de Azure-portal voor uw resource.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

De HTML-tag `<body>` bevat een kenmerk `onload` dat `getSearchSubscriptionKey()` en `getMapsSubscriptionKey()` aanroept wanneer de pagina is geladen. Deze aanroepen zorgen ervoor dat de gebruiker direct om zijn of haar sleutels wordt gevraagd als deze nog niet zijn ingevoerd.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Zoekopties selecteren

![[Formulier Bing Entiteiten zoeken]](media/entity-search-spa-form.png)

Het HTML-formulier bevat de volgende besturingselementen:

| Beheer | Beschrijving |
|-|-|
|`where`|Een vervolgkeuzelijst voor het selecteren van de markt (locatie en taal) die wordt gebruikt voor de zoekopdracht.|
|`query`|Het tekstveld voor het invoeren van de zoektermen.|
|`safe`|Een selectievakje waarmee de functie SafeSearch kan worden in- of uitgeschakeld (beperkt resultaten voor 'volwassenen')|
|`what`|Een menu om aan te geven of u wilt zoeken naar entiteiten, plaatsen of beide.|
|`mapquery`|Het tekstveld waarin de gebruiker een volledig of gedeeltelijk adres, een oriëntatiepunt, enzovoort kan invoeren om meer relevante resultaten te vinden met Bing Entiteiten zoeken.|

> [!NOTE]
> Resultaten voor plaatsen zijn momenteel alleen beschikbaar in de Verenigde Staten. De menu's `where` en `what` hebben code voor het afdwingen van deze beperking. Als u een niet-Amerikaanse markt kiest terwijl Places is geselecteerd in het menu `what`, wordt `what` gewijzigd in Anything. Als u Places kiest terwijl een niet-Amerikaanse markt is geselecteerd in het menu `where`, wordt `where` gewijzigd in US.

Onze JavaScript-functie `bingSearchOptions()` converteert deze velden naar een gedeeltelijke querytekenreeks voor de Bing Zoeken-API.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Zo kan de functie SafeSearch de waarde `strict`, `moderate` of `off` hebben, waarbij `moderate` de standaardwaarde is. Ons formulier gebruikt echter een selectievakje en selectievakjes hebben maar twee statussen. De JavaScript-code converteert deze instelling naar `strict` of `off` (`moderate` wordt niet gebruikt).

Het veld `mapquery` wordt niet verwerkt in `bingSearchOptions()` omdat dit wordt gebruikt voor de locatiequery van Bing Kaarten, niet voor Bing Entiteiten zoeken.

## <a name="obtaining-a-location"></a>Een locatie ophalen

De Bing Kaarten-API biedt een [`locationQuery`-methode](//msdn.microsoft.com/library/ff701711.aspx), die we gebruiken om de breedtegraad en lengtegraad te vinden van de locatie die de gebruiker invoert. Deze coördinaten worden vervolgens met de aanvraag van de gebruiker doorgegeven aan de Bing Entiteiten zoeken-API. In de lijst met zoekresultaten hebben entiteiten en plaatsen die zich dicht bij de opgegeven locatie bevinden prioriteit.

De Bing Kaarten-API is niet toegankelijk met een gewone `XMLHttpRequest`-query in een web-app omdat de service geen ondersteuning biedt voor cross-origin-query's. Gelukkig ondersteunt de service wel JSONP (de 'P' staat voor 'padded' of opgevuld). Een JSONP-antwoord is een gewoon JSON-antwoord dat is verpakt in een functieaanroep. De aanvraag wordt gedaan door een tag `<script>` in te voegen in het document. (Het laden van scripts is niet onderhevig aan het beveiligingsbeleid van browsers.)

De functie `bingMapsLocate()` maakt de tag `<script>` voor de query en voegt deze in. Het segment `jsonp=bingMapsCallback` van de querytekenreeks bevat de naam van de functie die moet worden aangeroepen met het antwoord.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Als de Bing Kaarten-API niet reageert, wordt de functie `bingMapsCallBack()` nooit aangeroepen. Normaal gesproken zou dit betekenen dat `bingEntitySearch()` niet wordt aangeroepen en er geen zoekresultaten voor entiteiten worden weergegeven. Om dit scenario te voorkomen, stelt `bingMapsLocate()` ook een timer in om `bingEntitySearch()` na vijf seconden aan te roepen. Er is logica opgenomen in de callback-functie om te voorkomen dat er twee keer wordt gezocht naar entiteiten.

Wanneer de query is voltooid, wordt de functie `bingMapsCallback()` aangeroepen, zoals aangevraagd.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Naast de breedtegraad en lengtegraad, heeft de Bing Entiteiten zoeken-query een *radius* nodig die de nauwkeurigheid van de locatiegegevens aangeeft. We berekenen de radius met behulp van het *begrenzingsvak* in het antwoord van Bing Kaarten. Het begrenzingsvak is een rechthoek die de hele locatie omsluit. Als de gebruiker bijvoorbeeld `NYC` invoert, bevat het resultaat centrale coördinaten van New York City en een selectiekader dat de stad omsluit. 

We berekenen eerst de afstand tussen de primaire coördinaten en elk van de vier hoeken van het begrenzingsvak met behulp van de functie `haversineDistance()` (niet weergegeven). We gebruiken de grootste van deze vier afstanden als de radius. De minimale radius is een kilometer. Deze waarde wordt ook gebruikt als een standaardwaarde als er geen begrenzingsvak is opgenomen in het antwoord.

Als we beschikken over de coördinaten en de radius, kunnen we `bingEntitySearch()` aanroepen om de werkelijke zoekopdracht uit te voeren.

## <a name="performing-the-search"></a>De zoekopdracht uitvoeren

Aan de hand van de query, een locatie, een tekenreeks met opties en de API-sleutel, voert de functie `BingEntitySearch()` de aanvraag van Bing Entiteiten zoeken uit.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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

Als de HTTP-aanvraag is voltooid, roept JavaScript onze gebeurtenis-handler `load` aan, de functie `handleBingResponse()`, om een geslaagde HTTP GET-aanvraag naar de API te verwerken. 

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Een juist voltooide HTTP-aanvraag betekent *niet* noodzakelijkerwijs dat de zoekopdracht zelf ook is geslaagd. Als er een fout optreedt in de zoekbewerking, wordt met de Bing Entiteiten zoeken-API een andere HTTP-statuscode dan 200 geretourneerd en bevat het JSON-antwoord de foutgegevens. Daarnaast wordt met de API een leeg antwoord geretourneerd als er beperkingen gelden voor de aanvraag.

Een groot deel van de code in beide voorgaande functies is toegewezen aan foutafhandeling. Er kunnen fouten optreden in de volgende fasen:

|Fase|Potentiële fout(en)|Verwerkt met|
|-|-|-|
|JavaScript-aanvraagobject samenstellen|Ongeldige URL|`try`/`catch`-blok|
|De aanvraag indienen|Netwerkfouten, afgebroken verbindingen|Gebeurtenis-handlers `error` en `abort`|
|De zoekopdracht uitvoeren|Ongeldige aanvraag, ongeldige JSON, geldende beperkingen|Tests in gebeurtenis-handler van `load`|

Fouten worden afgehandeld door `renderErrorMessage()` aan te roepen met eventuele bekende details over de fout. Als het antwoord de volledige set met fouttests doorgeeft, wordt `renderSearchResults()` aangeroepen om de zoekresultaten op de pagina weer te geven.

## <a name="displaying-search-results"></a>Zoekresultaten weergeven

De Bing Entiteiten zoeken-API [vereist dat u resultaten weergeeft in een bepaalde volgorde](../bing-web-search/use-display-requirements.md). Omdat de API twee verschillende soorten antwoorden kan retourneren, is het niet voldoende om de verzameling `Entities` of `Places` op het hoogste niveau in het JSON-antwoord te doorlopen en die resultaten weer te geven. (Als u slechts één type resultaat wilt, gebruikt u de queryparameter `responseFilter`.)

In plaats daarvan gebruiken we de verzameling `rankingResponse` in de zoekresultaten om de resultaten te ordenen voor weergave. Dit object verwijst naar items in de verzameling `Entitiess` en/of `Places`.

`rankingResponse` kan maximaal drie verzamelingen met zoekresultaten bevatten, aangeduid als `pole`, `mainline` en `sidebar`. 

`pole`, indien aanwezig, is het meest relevante zoekresultaat en moet duidelijk zichtbaar worden weergegeven. `mainline` verwijst naar het grootste deel van de zoekresultaten. Mainline-resultaten moeten direct `pole` worden weergegeven (of als eerste als `pole` niet aanwezig is). 

Als laatste verwijst `sidebar` naar aanvullende zoekresultaten. Deze kunnen worden weergegeven in een echte zijbalk of gewoon na de mainline-resultaten. We hebben de laatste optie gekozen voor onze zelfstudie-app.

Elk item in een verzameling `rankingResponse` verwijst naar de werkelijke zoekresultaatitems op twee verschillende, maar gelijkwaardige, manieren.

| Item | Beschrijving |
|-|-|
|`id`|De `id` ziet eruit als een URL, maar mag niet worden gebruikt voor koppelingen. Het type `id` van een positieresultaat komt overeen met de `id` van een zoekresultaatitem zoeken in een antwoordverzameling, *of* een complete antwoordverzameling (zoals `Entities`).
|`answerType`<br>`resultIndex`|Het `answerType` verwijst naar de antwoordverzameling op het hoogste niveau die het resultaat bevat (bijvoorbeeld `Entities`). De `resultIndex` verwijst naar de index van het resultaat in die verzameling. Als `resultIndex` wordt weggelaten, verwijst het positieresultaat naar de volledige verzameling.

> [!NOTE]
> Zie [Positie van resultaten bepalen](rank-results.md) voor meer informatie over dit onderdeel van het zoekantwoord.

U kunt de methode gebruiken die het best aansluit bij uw toepassing om te zoeken naar het item in de zoekresultaten waarnaar wordt verwezen. In de code in deze zelfstudie gebruiken we `answerType` en `resultIndex` om de zoekresultaten te vinden.

Als laatste kijken we naar de functie `renderSearchResults()`. Deze functie doorloopt de drie `rankingResponse`-verzamelingen die de drie secties van de zoekresultaten vertegenwoordigen. Voor elke sectie roepen we `renderResultsItems()` aan om de resultaten voor die sectie weer te geven.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Resultaten weergeven

Onze JavaScript-code bevat een object, `searchItemRenderers`, met daarin *renderers*: functies waarmee HTML wordt weergegeven voor elk type zoekresultaat.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Een rendererfunctie kan de volgende parameters accepteren:

| Parameter | Beschrijving |
|-|-|
|`item`|Het JavaScript-object met de eigenschappen van het item, zoals de bijbehorende URL en beschrijving.|
|`index`|De index van het resultaatitem binnen de bijbehorende verzameling.|
|`count`|Het aantal items in de verzameling van het zoekresultaatitem.|

De parameters `index` en `count` kunnen worden gebruikt om resultaten te nummeren, speciale HTML te genereren voor het begin en einde van een verzameling, regeleinden in te voegen na een bepaald aantal items, enzovoort. Als een renderer deze functionaliteit niet nodig heeft, hoeven deze twee parameters niet te worden geaccepteerd. We gebruiken ze dan ook niet in de renderers voor onze zelfstudie-app.

Laten we de renderer `entities` eens wat beter bekijken:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Met onze rendererfunctie worden de volgende handelingen uitgevoerd:

> [!div class="checklist"]
> * De HTML-tag `<img>` bouwen om de miniatuur van de afbeelding weer te geven, indien beschikbaar. 
> * Hiermee wordt de HTML-`<a>`-tag gebouwd die is gekoppeld aan de pagina die de afbeelding bevat.
> * De beschrijving met informatie over de afbeelding en de bijbehorende site wordt samengesteld.
> * De classificatie van de entiteit opnemen met behulp van de weergavehints, indien beschikbaar.
> * Een koppeling opnemen naar een zoekopdracht met Bing om meer informatie over de entiteit op te vragen.
> * Licentie- of toewijzingsgegevens weergeven die vereist zijn voor gegevensbronnen.

## <a name="persisting-client-id"></a>Permanente client-id

Antwoorden van de Bing Zoeken-API’s kunnen een `X-MSEdge-ClientID`-header omvatten die bij volgende aanvragen moet worden teruggestuurd naar de API. Als er meerdere Bing Search-API's worden gebruikt, moet voor al deze API's, indien mogelijk, dezelfde client-id worden gebruikt.

Door de `X-MSEdge-ClientID`-header op te geven kunnen met Bing-API's alle zoekopdrachten van een gebruiker worden gekoppeld. Dit heeft twee belangrijke voordelen.

Ten eerste kan met de Bing-zoekmachine vroegere context worden toegepast op zoekopdrachten om beter kloppende resultaten te vinden voor de gebruiker. Als een gebruiker bijvoorbeeld eerder heeft gezocht naar termen die zijn gerelateerd aan zeilen, kan bij een latere zoekopdracht naar ‘knopen’ de voorkeur worden gegeven aan informatie over knopen die worden gebruikt bij zeilen.

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
> [Naslaghandleiding Bing Entiteiten zoeken-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Documentatie Bing Kaarten-API](//msdn.microsoft.com/library/dd877180.aspx)
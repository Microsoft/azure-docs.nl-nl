---
title: 'Zelfstudie: Afbeelding uploaden met behulp van de Bing Visual Search-API'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het uploaden van een afbeelding in Bing, het verkrijgen van inzichten over de afbeelding, en het weergeven van het antwoord.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: 96a4b13d11e40e24e78d3aed8dfebcc88b41c525
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486875"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Zelfstudie: Afbeeldingen uploaden naar de Bing Visual Search-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Visual Search-API kunt u op internet zoeken naar afbeeldingen die overeenkomen met de afbeeldingen die u uploadt. Gebruik deze zelfstudie om een webtoepassing te maken waarmee een afbeelding naar de API kan worden verzonden en waarmee de inzichten die worden geretourneerd op de webpagina worden weergegeven. Deze toepassing voldoet niet aan alle [vereisten voor gebruik en weergave van Bing](../bing-web-search/use-display-requirements.md) voor het gebruik van de API.

U vindt de volledige broncode voor dit voorbeeld in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html) met extra foutafhandeling en aantekeningen.

In de zelfstudie-app leert u het volgende:

> [!div class="checklist"]
> * Een afbeelding uploaden naar de Bing Visual Search-API
> * Zoekresultaten voor afbeeldingen weergeven in een webtoepassing
> * De verschillende inzichten verkennen die worden geleverd door de API

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>De webpagina maken en structureren

Maak een HTML-pagina waarmee een afbeelding naar de Bing Visual Search-API wordt verzonden, en inzichten worden ontvangen en weergegeven. Maak in uw favoriete editor of IDE een bestand met de naam uploaddemo.html. Voeg de volgende eenvoudige HTML-structuur toe aan het bestand:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Verdeel de pagina in een aanvraagsectie waar de gebruiker alle informatie verschaft die is vereist om de aanvraag te doen, en een antwoordsectie waar de inzichten worden weergegeven. Voeg de volgende `<div>`-tags toe aan de `<body>`. De `<hr>`-tag scheidt de aanvraagsectie visueel van de antwoordsectie:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Voeg een `<script>`-tag toe aan de `<head>`-tag voor het JavaScript voor de toepassing:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Het uploadbestand ophalen

De toepassing gebruikt de `<input>`-tag met het kenmerk 'type' ingesteld op `file` om de gebruiker de te uploaden afbeelding te laten selecteren. De gebruikersinterface moet duidelijk maken dat de toepassing Bing gebruikt om de zoekresultaten te krijgen.

Voeg de volgende `<div>` toe in de `requestSection` `<div>`: De bestandsinvoer accepteert één bestand, van elk afbeeldingstype (bijvoorbeeld .jpg, .gif, .png). De `onchange`-gebeurtenis geeft de handler aan die wordt aangeroepen wanneer een gebruiker een bestand selecteert.

De `<output>`-tag wordt gebruikt om een ​​miniatuur van de geselecteerde afbeelding weer te geven:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Een bestandshandler maken

Maak een handlerfunctie die kan worden gelezen in de afbeelding die u wilt uploaden. Tijdens het doorlopen van de bestanden in het `FileList`-object moet de handler ervoor zorgen dat het geselecteerde bestand een afbeeldingsbestand is van 1 MB of minder. Als de afbeelding groter is, moet u de afbeelding kleiner maken voordat u deze uploadt. Ten slotte geeft de handler een miniatuur van de afbeelding weer:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Een abonnementssleutel toevoegen en opslaan

De toepassing heeft een abonnementssleutel nodig voor aanroepen naar de Bing Visual Search-API. In deze zelfstudie geeft u deze op in de gebruikersinterface. Voeg de volgende `<input>`-tag (met het kenmerk 'type' ingesteld op 'text') toe aan de `<body>`, vlak onder de `<output>`-tag van het bestand:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Aan de hand van de afbeelding en de abonnementssleutel kunt u Bing Visual Search aanroepen om inzicht te krijgen in de afbeelding. In deze zelfstudie gebruikt de aanroep de standaardwaarden voor de markt (`en-us`) en Veilig Zoeken (`moderate`).

Deze waarden kunnen in deze toepassing worden gewijzigd. Voeg de volgende `<div>` toe onder de abonnementssleutel `<div>`. De toepassing gebruikt een `<select>`-tag om een ​​vervolgkeuzelijst te bieden voor de waarden voor de markt en Veilig Zoeken. In beide lijsten wordt de standaardwaarde weergegeven.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Zoekopties doorgeven aan de webpagina

De toepassing verbergt de lijsten in een samenvouwbare `<div>` die wordt bestuurd met de koppeling Queryopties. Wanneer u op de koppeling Queryopties klikt, wordt de `<div>` uitgevouwen, zodat u de zoekopties kunt zien en wijzigen. Als u nogmaals op de koppeling Queryopties klikt, wordt de `<div>` samengevouwen en verborgen. In het volgende fragment ziet u de `onclick`-handler voor de koppeling Queryopties. De handler bepaalt of de `<div>` is uitgevouwen of samengevouwen. Voeg deze handler toe aan de `<script>`-sectie. De handler wordt gebruikt voor alle samenvouwbare `<div>`-secties in de demo.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>De `onclick`-handler aanroepen

Voeg de volgende knop `"Get insights"` toe onder de opties `<div>` in de hoofdtekst. Met deze knop kunt u de aanroep initiëren. Wanneer de gebruiker op de knop klikt, verandert de cursor in de draaiende wachtcursor en wordt de `onclick`-handler aangeroepen.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Voeg de `onclick`-handler van de knop, `handleQuery()`, toe aan de `<script>`-tag.

## <a name="handle-the-query"></a>De query verwerken

Met de handler `handleQuery()` wordt gecontroleerd of de abonnementssleutel aanwezig is en 32 tekens lang is, en of er een afbeelding is geselecteerd. Verder worden eventuele inzichten uit een eerdere query gewist. Daarna wordt de `sendRequest()`-functie aangeroepen om de aanroep uit te voeren.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>De zoekaanvraag verzenden

De `sendRequest()`-functie deelt de eindpunt-URL in, stelt de header `Ocp-Apim-Subscription-Key` in op de abonnementssleutel, voegt het binaire bestand van de afbeelding toe om te uploaden, geeft de antwoordhandler aan en voert de aanroep uit:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>De API-reactie ophalen en behandelen

De `handleResponse()`-functie behandelt het antwoord van de aanroep van Bing Visual Search. Als de aanroep slaagt, parseert deze functie de JSON-reactie in de afzonderlijke tags, die de inzichten bevatten. Vervolgens wordt de lijst met zoekresultaten toegevoegd aan de pagina. De toepassing maakt een samenvouwbare `<div>` voor elke tag om te bepalen hoeveel gegevens worden weergegeven. Voeg de handler toe aan de `<script>`-sectie.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Het antwoord parseren

De `parseResponse`-functie converteert het JSON-antwoord naar een woordenboekobject door `json.tags` te doorlopen.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Een tagsectie bouwen

De functie `buildTagSections()` doorloopt de geparseerde JSON-tags en roept de `buildDiv()`-functie aan om een `<div>` samen te stellen voor elke tag. Elke tag wordt weergegeven als een koppeling. Wanneer de gebruiker op de koppeling klikt, wordt de tag uitgevouwen en worden de inzichten weergegeven die aan de tag zijn gekoppeld. Wanneer de gebruiker opnieuw op de koppeling klikt, wordt de sectie samengevouwen.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>De zoekresultaten in de webpagina weergeven

De `buildDiv()`-functie roept de `addDivContent`-functie aan om de inhoud van het samenvouwbare `<div>` voor elke tag op te bouwen.

De inhoud van een tag omvat de JSON uit het antwoord voor de tag. Aanvankelijk worden alleen de eerste 100 tekens van de JSON weergegeven, maar u kunt op de JSON-tekenreeks klikken om de hele JSON te tonen. Als u hier nogmaals op klikt, wordt de JSON-tekenreeks weer samengevouwen tot 100 tekens.

Voeg vervolgens de in de tag gevonden actietypen toe. Roep voor elk actietype de juiste functies aan om de inzichten ervan toe te voegen:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Inzichten voor verschillende acties weergeven

De volgende functies geven inzichten voor verschillende acties weer. De functies bieden een afbeelding of koppeling waarop kan worden geklikt om naar een webpagina te gaan met meer informatie over de afbeelding. Deze pagina wordt gehost door Bing.com of de oorspronkelijke website van de afbeelding. Niet alle gegevens van de inzichten worden in deze toepassing weergegeven. Raadpleeg de verwijzing [Afbeeldingen - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) om alle beschikbare velden voor een inzicht te zien.

> [!NOTE]
> Op de pagina moet een minimale hoeveelheid inzichtgegevens worden weergegeven. Bekijk de [Vereisten voor gebruik en weergave van Bing Search-API](../bing-web-search/use-display-requirements.md) voor meer.

### <a name="relatedimages-insights"></a>RelatedImages-inzichten

Met de `addRelatedImages()`-functie maakt u een titel voor elke website waarop de gerelateerde afbeelding wordt gehost, door de lijst met `RelatedImages`-acties te doorlopen en een `<img>`-tag toe te voegen aan de buitenste `<div>` voor elke tag.

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PagesIncluding-inzichten

Met de `addPagesIncluding()`-functie maakt u een koppeling voor elke website waarop de geüploade afbeelding wordt gehost, door de lijst met `PagesIncluding`-acties te doorlopen en een `<img>`-tag toe te voegen aan de buitenste `<div>` voor elke tag.

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches-inzichten

Met de `addRelatedSearches()`-functie maakt u een koppeling voor de website waarop de afbeelding wordt gehost, door de lijst met `RelatedSearches`-acties te doorlopen en een `<img>`-tag toe te voegen aan de buitenste `<div>` voor elke tag.

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Recepten-inzichten

Met de `addRecipes()`-functie maakt u een koppeling voor elk recept dat wordt geretourneerd, door de lijst met `Recipes`-acties te doorlopen en een `<img>`-tag toe te voegen aan de buitenste `<div>` voor elke tag.

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Winkel-inzichten

Met de `addShopping()`-functie maakt u een koppeling voor elk winkelresultaat dat wordt geretourneerd, door de lijst met `RelatedImages`-acties te doorlopen en een `<img>`-tag toe te voegen aan de buitenste `<div>` voor elke tag.

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Product-inzichten

Met de `addProducts()`-functie maakt u een koppeling voor elk product dat wordt geretourneerd, door de lijst met `Products`-acties te doorlopen en een `<img>`-tag toe te voegen aan de buitenste `<div>` voor elke tag.

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult-inzichten

Met de `addTextResult()`-functie geeft u tekst weer die in de afbeelding is herkend:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

Met de `addEntity()`-functie geeft u een koppeling weer waarmee de gebruiker naar Bing.com gaat en informatie krijgt over het entiteitstype in de afbeelding, indien er een is gedetecteerd:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

Met de `addImageWithWebSearchUrl()`-functie geeft u een afbeelding weer waarop kan worden geklikt voor de `<div>` waarmee de gebruiker naar de zoekresultaten op Bing.com gaat:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Een CSS-stijl toevoegen

Voeg de volgende `<style>`-sectie toe aan de `<head>`-tag om de indeling van de webpagina aan te passen:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
> [Zelfstudie: Soortgelijke afbeeldingen vinden in eerdere zoekopdrachten met ImageInsightsToken](./tutorial-visual-search-insights-token.md)
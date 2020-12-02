---
title: " Een web-app met één pagina bouwen - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Hier leest u hoe u de Bing Visual Search-API kunt gebruiken in een webtoepassing met één pagina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b1ca88cd654b7bf373bee60b1e9b7a2e7a129fa2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499013"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Zelfstudie: Een Visual Search-web-app met één pagina maken

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Bing Visual Search-API retourneert inzichten over een afbeelding. U kunt een afbeelding uploaden of een URL naar een afbeelding opgeven. Inzichten zijn visueel vergelijkbare afbeeldingen, winkelbronnen, webpagina's die de afbeelding bevatten, enzovoort. Inzichten die door de Bing Visual Search-API worden geretourneerd, zijn vergelijkbaar met die van Bing.com/images.

In deze zelfstudie wordt uitgelegd hoe u een web-app met één pagina kunt uitbreiden voor gebruik met de Bing Video Search-API. Als u die zelfstudie wilt bekijken of de broncode wilt downloaden die in de zelfstudie wordt gebruikt, raadpleegt u [Zelfstudie: Een app van één pagina maken met de Bing Image Search-API](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

De volledige broncode voor deze toepassing (na uitbreiding van de toepassing voor het gebruik van de Bing Visual Search-API) is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>De Bing Visual Search-API aanroepen en het antwoord verwerken

Bewerk de zelfstudie Bing Image Search en voeg de volgende code toe aan het einde van het element `<script>` (vóór de afsluitende tag `</script>`). Met de volgende code wordt een antwoord van de API op een visuele zoekopdracht verwerkt, worden de resultaten doorlopen en worden ze daarna weergegeven:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Met de volgende code wordt een zoekopdracht naar de API verzonden, met behulp van een gebeurtenislistener om `handleVisualSearchResponse()` aan te roepen:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Inzichttoken vastleggen

Voeg de volgende code toe aan het object `searchItemsRenderer`. Deze code voegt de koppeling **find similar** toe (vergelijkbare zoeken) die de `bingVisualSearch`-functie aanroept wanneer erop wordt geklikt. De functie ontvangt het `imageInsightsToken` als argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Vergelijkbare afbeeldingen weergeven

Voeg de volgende HTML-code toe op regel 601. Deze code voegt een element toe voor het weergeven van de resultaten van de Bing Visual Search-API-aanroep:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Met alle nieuwe JavaScript-code en HTML-elementen op hun plaats, worden zoekresultaten weergegeven met een koppeling **find similar** (vergelijkbare zoeken). Klik op koppeling om de sectie **Similar** (vergelijkbaar) te vullen met afbeeldingen die lijken op de afbeelding die u hebt gekozen. Mogelijk moet u de sectie **Similar** uitvouwen om de afbeeldingen weer te geven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een afbeelding bijsnijden met de Bing Visual Search-SDK voor C#](tutorial-visual-search-crop-area-results.md)
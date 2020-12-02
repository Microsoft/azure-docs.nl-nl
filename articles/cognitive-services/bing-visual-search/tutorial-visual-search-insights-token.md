---
title: Vergelijkbare afbeeldingen als in vorige zoekopdrachten vinden met behulp van inzichttokens en de Bing Visual Search-API
titleSuffix: Azure Cognitive Services
description: Gebruik de Bing Visual Search-clientbibliotheek om URL's van afbeeldingen van vorige zoekopdrachten op te halen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a9005082148803423ac20b4f18dd974bb5b43b80
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486858"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Zelfstudie: Vergelijkbare afbeeldingen als in vorige zoekopdrachten vinden met behulp van inzichttokens

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Visual Search-clientbibliotheek kunt u online afbeeldingen vinden op basis van eerdere zoekopdrachten die een `ImageInsightsToken` retourneren. Met deze toepassing wordt een `ImageInsightsToken` opgehaald en wordt het token gebruikt in een volgende zoekopdracht. Vervolgens wordt de `ImageInsightsToken` naar Bing verzonden en worden resultaten geretourneerd met onder meer Bing Search-URL's en URL's van vergelijkbare, online gevonden afbeeldingen.

De volledige broncode voor deze zelfstudie is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs) met extra foutafhandeling en aantekeningen.

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Als u Linux/MacOS gebruikt, kunt u deze toepassing uitvoeren met behulp van [Mono](https://www.mono-project.com/).
* De pakketten NuGet Visual Search en Image Search.
    - Klik in Solution Explorer in Visual Studio met de rechtermuisknop op uw project en selecteer in het menu **NuGet-pakketten beheren**. Installeer het `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-pakket en het `Microsoft.Azure.CognitiveServices.Search.ImageSearch`-pakket. Als u de NuGet-pakketten installeert, worden ook de volgende onderdelen geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>ImageInsightsToken ophalen uit de Bing Image Search-clientbibliotheek

Deze toepassing maakt gebruik van een `ImageInsightsToken` die is verkregen via de [Bing Image Search-clientbibliotheek](../bing-image-search/quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp). Maak in een nieuwe C#-consoletoepassing een client om de API aan te roepen met behulp van `ImageSearchClient()`. Gebruik vervolgens `SearchAsync()` met uw query:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Sla het eerste zoekresultaat op met behulp van `imageResults.Value.First()` en sla de `ImageInsightsToken` van de inzichten van de afbeelding op.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Deze `ImageInsightsToken` wordt in een aanvraag naar Bing Visual Search verzonden.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>ImageInsightsToken aan een Visual Search-aanvraag toevoegen

Geef de `ImageInsightsToken` op voor een Visual Search-aanvraag door een `ImageInfo`-object te maken op basis van de `ImageInsightsToken` in reacties van Bing Visual Search.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Bing Visual Search gebruiken om afbeeldingen te zoeken vanuit een ImageInsightsToken

Het `VisualSearchRequest`-object bevat informatie over de afbeelding in `ImageInfo` die moet worden gezocht. Met de `VisualSearchMethodAsync()`-methode worden de resultaten opgehaald. U hoeft geen binair bestand van de afbeelding op te geven, omdat de afbeelding door het token wordt gerepresenteerd.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>De Visual Search-resultaten doorlopen

Visual Search-resultaten zijn `ImageTag`-objecten. Elke tag bevat een lijst met `ImageAction`-objecten. Elk object van het type `ImageAction` bevat een `Data`-veld. Dit is een lijst met waarden die afhankelijk zijn van het type actie. U kunt bijvoorbeeld de `ImageTag`-objecten in `visualSearchResults.Tags` doorlopen en de `ImageAction`-tag erin ophalen. In het onderstaande voorbeeld worden de details van `PagesIncluding`-acties weergegeven:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Voor het ophalen van de werkelijke afbeeldings-URL's van actietypen is een cast vereist die een `ActionType` leest als `ImageModuleAction`, die een `Data`-element met een waardelijst bevat. Elke waarde is de URL van een afbeelding.  De volgende code cast het actietype `PagesIncluding` naar `ImageModuleAction` en leest de waarde:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Zie [Afbeeldingen - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) voor meer informatie over deze gegevenstypen.

## <a name="returned-urls"></a>Geretourneerde URL's

De volledige toepassing retourneert de volgende URL's:

|ActionType  |URL  |
|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2f www.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2f www.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

De typen `TopicResults` en `ImageResults` bevatten query's voor gerelateerde afbeeldingen, zoals hierboven weergegeven. De URL's zijn koppelingen naar Bing-zoekresultaten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Visual Search-web-app met één pagina maken](tutorial-bing-visual-search-single-page-app.md)
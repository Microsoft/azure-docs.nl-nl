---
title: 'Zelfstudie: Een afbeelding bijsnijden met de Bing Visual Search-SDK'
description: Gebruik de Bing Visual Search-SDK om inzichten te verkrijgen van bepaalde gebieden in een afbeelding.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ad73d7086a2066517c24a8b10a7f54503535942
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490292"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Zelfstudie: Een afbeelding bijsnijden met de Bing Visual Search-SDK voor C#

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Visual Search-SDK kunt u een afbeelding bijsnijden voordat u vergelijkbare onlineafbeeldingen kunt vinden. Met deze toepassing wordt één persoon uit een afbeelding met meerdere personen gesneden. Vervolgens worden de zoekresultaten geretourneerd met vergelijkbare afbeeldingen die online zijn gevonden.

De volledige broncode voor deze toepassing is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs) met extra foutafhandeling en aantekeningen.

In deze zelfstudie ziet u hoe u de volgende zaken kunt uitvoeren:

> [!div class="checklist"]
> * Een aanvraag verzenden met de Bing Visual Search-SDK
> * Het deel van een afbeelding dat u zoekt, bijsnijden met Bing Visual Search
> * Het antwoord ontvangen en verwerken
> * De URL's van actie-items in het antwoord zoeken

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
* Het [NuGet-pakket Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) is geïnstalleerd.
    - Klik in Solution Explorer in Visual Studio met de rechtermuisknop op uw project en selecteer in het menu **NuGet-pakketten beheren**. Installeer het `Microsoft.Azure.CognitiveServices.Search.CustomSearch`-pakket. Met de installatie van het NuGet-pakket Custom Search worden ook de volgende assembly's geïnstalleerd:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Het bij te snijden gedeelte van de afbeelding opgeven

Met deze toepassing wordt een deel van deze afbeelding van het seniormanagementteam van Microsoft bijgesneden. Dit bij te snijden gedeelte wordt gedefinieerd aan de hand van de linker- en rechtercoördinaten, uitgedrukt als een percentage van de hele afbeelding:  

![Senior leidinggevenden bij Microsoft](./media/MS_SrLeaders.jpg)

Deze afbeelding wordt bijgesneden door een `ImageInfo`-object te maken op basis van het bij te snijden gedeelte en het `ImageInfo`-object in een `VisualSearchRequest` te laden. Het `ImageInfo`-object bevat ook de URL van de afbeelding:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Zoeken naar afbeeldingen die lijken op het bij te snijden gedeelte

De variabele `VisualSearchRequest` bevat informatie over het bij te snijden deel in de afbeelding en de bijbehorende URL. Met de `VisualSearchMethodAsync()`-methode worden de resultaten opgehaald:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>De URL-gegevens ophalen van `ImageModuleAction`

Resultaten van Bing Visual Search zijn `ImageTag`-objecten. Elke tag bevat een lijst met `ImageAction`-objecten. Elk object van het type `ImageAction` bevat een `Data`-veld. Dit is een lijst met waarden die afhankelijk zijn van het type actie.

U kunt de verschillende typen afdrukken met de volgende code:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

De volledige toepassing retourneert:

|ActionType  |URL  |
|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2f www.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2f www.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2f www.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Zoals hierboven weergegeven, bevat `Entity` ActionType een Bing-zoekquery waarmee informatie wordt geretourneerd over een herkenbare persoon, een herkenbare plaats of een herkenbaar ding. De typen `TopicResults` en `ImageResults` bevatten zoekopdrachten naar gerelateerde afbeeldingen. De URL's in de lijst zijn koppelingen naar Bing-zoekresultaten.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>URL's voor `PagesIncluding` `ActionType`-afbeeldingen ophalen

Voor het ophalen van de werkelijke afbeeldings-URL's is een cast vereist die een `ActionType` leest als `ImageModuleAction`, die een `Data`-element met een waardelijst bevat. Elke waarde is de URL van een afbeelding. De volgende code cast het actietype `PagesIncluding` naar `ImageModuleAction` en leest de waarde:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een Visual Search-web-app met één pagina maken](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Zie ook
> [Wat is de Bing Visual Search-API?](./overview.md)
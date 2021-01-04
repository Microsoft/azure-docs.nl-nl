---
title: Quickstart voor Bing Image Search-clientbibliotheek voor C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: e051eca990ae0aa0b5a79c208a594e1b2332bcb2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612624"
---
Gebruik deze quickstart om uw eerste afbeelding te zoeken met behulp van de Bing Image Search-clientbibliotheek. 

De zoekbibliotheek van de client is een wrapper voor de REST API en bevat dezelfde functies. 

Deze door u gemaakte C#-toepassing verzendt een zoekquery voor afbeeldingen, parseert het JSON-antwoord en geeft de URL weer van de eerst geretourneerde afbeelding.

De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) met extra foutafhandeling en aantekeningen.

## <a name="prerequisites"></a>Vereisten

* Als u met Windows werkt, kunt u een versie van [Visual Studio 2017 of hoger gebruiken](https://visualstudio.microsoft.com/vs/whatsnew/)
* Als u macOS of Linux gebruikt, kunt u [VS code](https://code.visualstudio.com) gebruiken met [.NET core geïnstalleerd](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [Een gratis Azure-abonnement](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Zie ook [Prijsinformatie Cognitive Services - Bing Zoeken-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Een consoleproject maken

Maak een nieuwe C#-consoletoepassing.

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Maak een nieuwe consoleoplossing met de naam *BingImageSearch* in Visual Studio.
    
1. Het [Cognitieve Afbeeldingen zoeken NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) toevoegen
    1. Klik met de rechtermuisknop op uw project in **Solution Explorer**.
    1. Selecteer **NuGet-pakketten beheren**.
    1. Zoek en selecteer *Microsoft.Azure.CognitiveServices.Search.ImageSearch* en installeer vervolgens het pakket.
    
# <a name="vs-code"></a>[VS-code](#tab/vscode)

1. Open het terminalvenster in VS code.
1. Maak een nieuw consoleproject met de naam *BingImageSearch* door de volgende code in het terminalvenster in te voeren:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Open de map *BingImageSearch* in VS code.
1. Voeg het [Cognitive Image Search NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) toe door de volgende code in het terminalvenster in te voeren:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>De toepassing initialiseren


1. Vervang alle `using`-instructies in *Program.cs* met de volgende code:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. Maak in de `Main`-methode van uw project variabelen voor uw geldige abonnementssleutel, de afbeeldingsresultaten die moeten worden geretourneerd door Bing en een zoekterm. Maak vervolgens met behulp van de sleutel een instantie van de client voor het zoeken van afbeeldingen.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Een zoekquery verzenden met behulp van de client
    
Gebruik in de `Main`-methode de client om te zoeken met een querytekst:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Het eerste afbeeldingsresultaat

Parseer de afbeeldingsresultaten die in het antwoord zijn geretourneerd. 

Als het antwoord zoekresultaten bevat, slaat u het eerste resultaat op en drukt u een aantal details af.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie voor app met één pagina voor Bing Image Search](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zie ook

* [Wat is Bing Image Search?](../../overview.md)  
* [Online interactieve demo proberen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [.NET-voorbeelden voor de Azure Cognitive Services-SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Documentatie van Azure Cognitive Services](../../../index.yml)
* [Naslag voor Bing Afbeeldingen zoeken-API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
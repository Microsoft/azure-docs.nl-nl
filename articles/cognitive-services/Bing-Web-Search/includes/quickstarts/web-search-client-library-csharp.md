---
title: Quickstart voor Bing Web Search-clientbibliotheek voor C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: ff4a29cd2da98d6782d2e3bae5078e92bc43eaca
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880981"
---
Met de Bing Web Search-clientbibliotheek kunt u Bing Web Search eenvoudig integreren in uw C#-toepassing. In deze snelstartgids leert u hoe u een instantie maakt voor een client, een aanvraag verzendt en het antwoord weergeeft.

Wilt u de code nu zien? Voorbeelden voor de [Bing Search-clientbibliotheken voor .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) zijn beschikbaar op GitHub.

## <a name="prerequisites"></a>Vereisten
Voordat u verdergaat met deze snelstart moet u beschikken over:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) of
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# voor Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [NuGet Package Manager](https://github.com/jmrog/vscode-nuget-package-manager)
* [.NET Core-SDK](https://dotnet.microsoft.com/download)

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-install-dependencies"></a>Een project maken en afhankelijkheden installeren

> [!TIP]
> Haal de nieuwste code als Visual Studio-oplossing op uit [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/).

De eerste stap bestaat uit het maken van een nieuw consoleproject. Als u hulp nodig hebt bij het opzetten van een consoleproject, raadpleegt u [Hello World: uw eerste programma (C#-programmeerhandleiding)](/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Als u de Bing Web Search SDK wilt gebruiken in uw toepassing, moet u `Microsoft.Azure.CognitiveServices.Search.WebSearch` installeren met behulp van NuGet Package Manager.

Met het [Web Search SDK-pakket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) wordt ook het volgende geïnstalleerd:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Afhankelijkheden declareren

Open uw project in Visual Studio of Visual Studio Code en importeer de volgende afhankelijkheden:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
using System.Threading.Tasks;
```

## <a name="create-project-scaffolding"></a>Een projectopzet maken

Wanneer u het nieuwe consoleproject hebt gemaakt, moeten er een naamruimte en klasse voor uw toepassing zijn gemaakt. Uw programma moet eruitzien als dit voorbeeld:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

In de volgende secties maken we onze voorbeeldtoepassing in deze klasse.

## <a name="construct-a-request"></a>Een aanvraag samenstellen

Met deze code wordt de zoekquery samengesteld.

```csharp
public static async Task WebResults(WebSearchClient client)
{
    try
    {
        var webData = await client.Web.SearchAsync(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Het antwoord verwerken

Laten we vervolgens code toevoegen voor het parseren van het antwoord en het weergeven van de resultaten. De `Name` en `Url` voor de eerste webpagina, de eerste afbeelding, het eerste nieuwsartikel en de eerste video worden weergegeven wanneer ze voorkomen in het antwoordobject.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>De hoofdmethode declareren

In deze toepassing bevat de hoofdmethode code waarmee een instantie wordt gemaakt voor de client, de `subscriptionKey` wordt gevalideerd en `WebResults` wordt aangeroepen. Zorg ervoor dat u een geldige abonnementssleutel voor uw Azure-account invoert voordat u verdergaat.

```csharp
static async Task Main(string[] args)
{
    var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    await WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Nu gaan we de toepassing uitvoeren.

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Functies definiëren en resultaten filteren

Nu u voor het eerst de Bing Webzoekopdrachten-API hebt aangeroepen, kunnen we enkele functies bekijken die een voorbeeld zijn van SDK-functionaliteit voor het verfijnen van query's en het filteren van resultaten. Elke functie kan worden toegevoegd aan uw C#-toepassing die in de vorige sectie is gemaakt.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Het aantal resultaten beperken dat door Bing wordt geretourneerd

In dit voorbeeld worden de parameters `count` en `offset` gebruikt voor het beperken van het aantal resultaten dat voor 'Beste restaurants in Seattle' wordt geretourneerd. De `Name` en `Url` voor het eerste resultaat worden weergegeven.

1. Voeg deze code toe aan uw consoleproject:

    ```csharp
    public static async Task WebResultsWithCountAndOffset(WebSearchClient client)
    {
        try
        {
            var webData = await client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20);
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Voeg `WebResultsWithCountAndOffset` toe aan `main`:

    ```csharp
    static async Task Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        await WebResults(client);
        // Search with count and offset...
        await WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Voer de toepassing uit.

### <a name="filter-for-news"></a>Filteren op nieuws

In dit voorbeeld wordt de parameter `response_filter` gebruikt om zoekresultaten te filteren. De geretourneerde zoekresultaten zijn beperkt tot nieuwsartikelen voor 'Microsoft'. De `Name` en `Url` voor het eerste resultaat worden weergegeven.

1. Voeg deze code toe aan uw consoleproject:

    ```csharp
    public static async Task WebSearchWithResponseFilter(WebSearchClient client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = await client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings);
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Voeg `WebResultsWithCountAndOffset` toe aan `main`:

    ```csharp
    static Task Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        await WebResults(client);
        // Search with count and offset...
        await WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        await WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Voer de toepassing uit.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Gebruik de parameters voor veilig zoeken, aantal antwoorden en het filter voor het promoten van zoekresultaten

In dit voorbeeld worden de parameters `answer_count`, `promote`, en `safe_search` gebruikt om de zoekresultaten voor 'muziekvideo's' te filteren. De `Name` en `ContentUrl` voor het eerste resultaat worden weergegeven.

1. Voeg deze code toe aan uw consoleproject:

    ```csharp
    public static async Task WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchClient client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = await client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict);
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Voeg `WebResultsWithCountAndOffset` toe aan `main`:

    ```csharp
    static async Task Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        await WebResults(client);
        // Search with count and offset...
        await WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        await WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        await WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Voer de toepassing uit.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met dit project, moet u uw abonnementssleutel verwijderen uit de code van de toepassing.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Voorbeelden voor Cognitive Services Node.js SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)

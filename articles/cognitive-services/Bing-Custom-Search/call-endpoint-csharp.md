---
title: 'Quickstart: Aanroepen van uw Bing Custom Search-eindpunt met C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om te beginnen met het opvragen van zoekresultaten van uw Bing Custom Search-exemplaar in C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: b892194a0e716aa3de218bc6edb6c38cdc898935
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338653"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Quickstart: Aanroepen van uw Bing Custom Search-eindpunt met C# 

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Lees in deze quickstart hoe u zoekresultaten opvraagt van de Bing Custom Search-instantie. Hoewel deze toepassing is geschreven in C#, is de Bing Custom Search-API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Vereisten

- Een Bing Custom Search-exemplaar. Zie voor meer informatie [Snelstart: Uw eerste Bing Custom Search-instantie maken](quick-start.md).
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core).
- Een versie van [Visual Studio 2019 of hoger](https://www.visualstudio.com/downloads/).
- Als u Linux/MacOS gebruikt, kan deze toepassing worden uitgevoerd met behulp van [Mono](https://www.mono-project.com/).
- Het NuGet-pakket [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0). 

   U kunt dit pakket als volgt installeren in Visual Studio: 
     1. Klik met de rechtermuisknop op het project in **Solution Explorer** en selecteer vervolgens **NuGet-pakketten beheren**. 
     2. Zoek en selecteer *Microsoft.Azure.CognitiveServices.Search.CustomSearch* en installeer vervolgens het pakket.

   Wanneer u het NuGet-pakket Bing Custom Search installeert, installeert Visual Studio ook de volgende pakketten:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak in Visual Studio een nieuwe C#-consoletoepassing. Voeg vervolgens de volgende pakketten toe aan uw project:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Maak de volgende klassen om de zoekresultaten op te slaan die door de Bing Custom Search-API worden geretourneerd:

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. Maak in de hoofdmethode van uw project de volgende variabelen voor uw abonnementssleutel van de Bing Custom Search-API, de aangepaste configuratie-ID van de zoekinstantie en een zoekterm:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Stel de aanvraag-URL samen door uw zoekterm toe te voegen aan de queryparameter `q=`, en de aangepaste configuratie-id van uw zoekinstantie aan de parameter `customconfig=`. Scheid de parameters van elkaar met een en-teken (`&`). Voor de variabele waarde voor `url` kunt u het globale eindpunt in de volgende code gebruiken of het eindpunt voor het [aangepaste subdomein](../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat voor uw resource wordt weergegeven in Azure Portal.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Een zoekaanvraag verzenden en ontvangen 

1. Maak een aanvraag-client, en voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Voer de zoekaanvraag uit en haal de reactie op als een JSON-object.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>De resultaten verwerken en weergeven

- Doorloop het reactieobject om informatie weer te geven over elk zoekresultaat, waaronder de naam, de URL en de datum waarop de webpagina voor het laatst is gecrawld.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](./tutorials/custom-search-web-page.md)
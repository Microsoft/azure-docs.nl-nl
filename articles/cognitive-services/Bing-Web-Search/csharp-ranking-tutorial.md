---
title: Classificeren gebruiken om zoekresultaten weer te geven
titleSuffix: Azure Cognitive Services
description: Laat zien hoe u het Bing RankingResponse-antwoord kunt gebruiken om zoekresultaten weer te geven op volgorde van prioriteit.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 907899d8c5be2cbba7ff3eee4a59713718357a5c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430085"
---
# <a name="build-a-console-app-search-client-in-c"></a>Een zoekclient voor console-app maken in C#

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Deze zelfstudie laat zien hoe u een eenvoudige .NET Core-console-app bouwt waarmee gebruikers query's kunnen uitvoeren op de Bing Web Search-API en gerangschikte resultaten kunnen weergeven.

In deze zelfstudie ontdekt u hoe u:

- Een eenvoudige query verstuurt naar de Bing Web Search-API
- Queryresultaten weergeeft in gerangschikte volgorde

## <a name="prerequisites"></a>Vereisten

Als u de zelfstudie wilt volgen, hebt u het volgende nodig:

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Een Bing Search-resource maken"  target="_blank">maakt u een Bing Search-resource </a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
* De [Visual Studio IDE](https://www.visualstudio.com/downloads/).

## <a name="create-a-new-console-app-project"></a>Een nieuw Console-appproject maken

Maak in Visual Studio een project met `Ctrl`+`Shift`+`N`.

Klik in het dialoogvenster **Nieuw project** op **Visual C# > Windows klassiek bureaublad > Console-app (.NET Framework)** .

Geef de toepassing de naam **myConsoleSearchApp** en klik vervolgens op **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Het JSON.net NuGet-pakket aan het project toevoegen

Met JSON.net kunt u werken met de JSON-antwoorden die zijn geretourneerd door de API. Voeg het bijbehorende NuGet-pakket toe aan uw project:

- Klik in **Solution Explorer** met de rechtermuisknop op het project en klik op **NuGet-pakketten beheren…** .
- Zoek op het tabblad **Bladeren** naar `Newtonsoft.Json`. Selecteer de nieuwste versie en klik vervolgens op **Installeren**.
- Klik op de knop **OK** in het venster **Wijzigingen bekijken**.
- Sluit het Visual Studio-tabblad met de titel **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Een verwijzing naar System.Web toevoegen

In deze zelfstudie wordt gebruikgemaakt van de `System.Web`-assembly. Voeg een verwijzing toe naar deze assembly aan uw project:

- Klik in **Solution Explorer** met de rechtermuisknop op **Verwijzingen** en selecteer **Verwijzing toevoegen…** .
- Selecteer **Assembly's > Framework**, schuif omlaag en controleer **System.Web**
- Selecteer **OK**

## <a name="add-some-necessary-using-statements"></a>Enkele vereiste instructies toevoegen

Voor de code in deze zelfstudie zijn drie extra instructies vereist. Voeg deze instructies toe onder de bestaande `using`-instructies bovenaan **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>De gebruiker vragen om een query

In **Solution Explorer** opent u **Program.cs**. Werk de `Main()`-methode bij:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Deze methode:

- De gebruiker wordt gevraagd om een query
- Roept `RunQueryAndDisplayResults(userQuery)` aan om de query uit te voeren en de resultaten weer te geven
- Wacht op invoer van de gebruiker om te voorkomen dat het consolevenster onmiddellijk wordt gesloten.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Zoeken naar queryresultaten met behulp van de Bing Web Search-API

Voeg vervolgens een methode toe waarmee query's worden uitgevoerd op de API en de resultaten worden weergegeven:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Deze methode:

- Hiermee maakt u een `HttpClient` om query's uit te voeren op de Web Search-API
- Hiermee stelt u de `Ocp-Apim-Subscription-Key` HTTP-header in die door Bing wordt gebruikt om de aanvraag te verifiëren
- Voert de aanvraag uit en gebruikt JSON.net om de resultaten te deserialiseren
- Roept `DisplayAllRankedResults(responseObjects)` aan om alle resultaten in de gerangschikte volgorde weer te geven

Zorg ervoor dat u de waarde van `Ocp-Apim-Subscription-Key` instelt op de sleutel van uw abonnement.

## <a name="display-ranked-results"></a>Gerangschikte resultaten weergeven

Bekijk een voorbeeld van een reactie op een webzoekopdracht voordat u kijkt naar hoe de resultaten in gerangschikte volgorde kunnen worden weergegeven:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

In het `rankingResponse` JSON-object ([documentatie](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) wordt de juiste weergavevolgorde voor zoekresultaten beschreven. Het bevat een of meer van de volgende groepen met prioriteit:

- `pole`: De zoekresultaten voor het verkrijgen van de meest zichtbare behandeling (bijvoorbeeld weergegeven boven de mainline en navigatiekolom).
- `mainline`: De zoekresultaten worden weergegeven in de mainline.
- `sidebar`: De zoekresultaten worden weergegeven in de navigatiekolom. Als er geen navigatiekolom is, geeft u de resultaten onder de mainline weer.

De JSON van het classificatie-antwoord kan een of meer van de groepen bevatten.

Voeg in **Program.cs** de volgende methode toe om de resultaten weer te geven in de juiste volgorde:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Deze methode:

- Lussen over de `rankingResponse`-groepen die het antwoord bevat
- De items in elke groep worden weergegeven door `DisplaySpecificResults(...)` aan te roepen

Voeg de volgende twee methoden toe in **Program.cs**:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Deze methoden werken samen om de zoekresultaten uit te voeren naar de console.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit. De uitvoer moet er als volgt uitzien:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [classificatie gebruiken om resultaten weer te geven](rank-results.md).
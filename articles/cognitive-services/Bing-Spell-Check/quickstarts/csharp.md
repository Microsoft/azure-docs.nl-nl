---
title: 'Quickstart: Spellingcontrole met de REST API en C# - Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Aan de slag met de Bing Spellingcontrole-REST API en C# om spelling en grammatica te controleren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: ace7a0ccaba533c9e72961536159d32af5ab8d98
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352725"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Quickstart: Spellingcontrole met de Bing Spellingcontrole REST API en C#

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Deze eenvoudige C#-toepassing verzendt een aanvraag naar de API en retourneert een lijst met voorgestelde correcties. 

Hoewel deze toepassing is geschreven in C#, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal. De broncode voor deze toepassing is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2017 of hoger](https://www.visualstudio.com/downloads/).
* Het pakket Newtonsoft.Json NuGet. 
     
   U kunt dit pakket als volgt installeren in Visual Studio:

     1. Klik in **Solution Explorer** met de rechtermuisknop op het bestand Solution.
     1. Selecteer **NuGet-pakketten beheren voor Solution**.
     1. Zoek vervolgens naar *Newtonsoft.Json* en installeer het pakket.

* Als u Linux/MacOS gebruikt, kunt u deze toepassing uitvoeren met behulp van [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuwe console-oplossing met de naam SpellCheckSample in Visual Studio. Voeg de volgende naamruimten in het hoofdcodebestand in:
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Maak variabelen voor het API-eindpunt, uw abonnementssleutel en de tekst die moet worden gecontroleerd. U kunt het globale eindpunt in de volgende code gebruiken of het eindpunt voor een [aangepast subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat wordt weergegeven in de Azure-portal voor uw resource.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Maak een tekenreeks voor uw zoekparameters: 

   1. Wijs uw marktcode toe aan de parameter `mkt` met de operator `=`. De marktcode is de code van het land of de regio van waaruit u de aanvraag indient. 

   1. Voeg de parameter `mode` met de `&` operator toe en wijs vervolgens de spellingcontrolemodus toe. De modus kan de waarde `proof` (de meeste spelling- en grammaticafouten worden onderschept) of de waarde `spell` (de meeste spellingfouten worden onderschept, maar niet zo veel grammaticafouten) zijn.
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Een spellingcontroleaanvraag maken en verzenden

1. Maken van een asynchrone functie met de naam `SpellCheck()` om een aanvraag te verzenden naar de API. Maak een `HttpClient`, en voeg uw abonnementssleutel toe aan de header `Ocp-Apim-Subscription-Key`. Volg de volgende stappen binnen de functie.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Maak de URI voor uw aanvraag door de host, het pad en de parameters toe te voegen.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Maak een lijst met een object `KeyValuePair` met uw tekst en gebruik het voor het maken van een object `FormUrlEncodedContent`. Stel de headerinformatie in en gebruik `PostAsync()` om de aanvraag te verzenden.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>De API-reactie ophalen en afdrukken

### <a name="get-the-client-id-header"></a>De client-ID-header ophalen

Als het antwoord een koptekst `X-MSEdge-ClientID` bevat, verkrijg dan de waarde en druk deze af.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Haal het antwoord op

Haal het antwoord op uit de API. Deserialiseer het JSON-object en druk het af naar de console.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Roep de spellingcontrolefunctie aan

In de `Main()`-functie van uw project roept u `SpellCheck()` aan.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Bouw uw project en voer het uit. Als u Visual Studio gebruikt, drukt u op **F5** om fouten in het bestand op te sporen.

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorials/spellcheck.md)

- [Wat is de Bing Spellingcontrole-API?](../overview.md)
- [Referentie voor de Bing Spellingcontrole-API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
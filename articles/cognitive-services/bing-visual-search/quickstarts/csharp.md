---
title: 'Quickstart: Afbeeldingsinzichten verkrijgen met de REST API en C# - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Leer hoe u een afbeelding kunt uploaden, met behulp van de Bing Visual Search-API en C#, en vervolgens inzichten in de afbeelding kunt verkrijgen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-csharp
ms.openlocfilehash: 83e4e2b1c1eb10f7a4cd0f2fb4f87b280801eed0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487113"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Quickstart: Afbeeldingsinzichten krijgen met behulp van de Bing Visual Search REST-API en C#

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

In deze quickstart wordt beschreven hoe u een afbeelding uploadt naar de Bing Visual Search-API en hoe u de inzichten weergeeft die door de API worden geretourneerd.

## <a name="prerequisites"></a>Vereisten

* Een versie van [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Het [Json.NET-framework](https://www.newtonsoft.com/json), beschikbaar als NuGet-pakket.
* Als u Linux/MacOS gebruikt, kunt u deze toepassing uitvoeren met [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak in Visual Studio een nieuwe consoleoplossing met de naam BingSearchApisQuickStart. Voeg de volgende naamruimten toe aan het hoofdcodebestand:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Voeg variabelen toe voor de abonnementssleutel, het eindpunt en het pad naar de afbeelding die u wilt uploaden. Voor de `uriBase`-waarde kunt u het globale eindpunt in de volgende code gebruiken of het eindpunt voor het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) gebruiken dat voor uw resource wordt weergegeven in Azure Portal.

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Maak een methode met de naam `GetImageFileName()` om het pad voor uw afbeelding op te halen.
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Maak een methode voor het ophalen van de binaire gegevens van de afbeelding.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>De formuliergegevens maken

1. Als u een lokale afbeelding wilt uploaden, moet u eerst de formuliergegevens maken om deze naar de API te verzenden. De formuliergegevens bevatten de `Content-Disposition`-header, de parameter `name` die is ingesteld op 'afbeelding' en de parameter `filename` die is ingesteld op de bestandsnaam van de afbeelding. De inhoud van het formulier bevat de binaire gegevens van de afbeelding. De maximale afbeeldingsgrootte die u kunt uploaden is 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. Voeg grenstekenreeksen toe om de POST-formuliergegevens op te maken. Met grenstekenreeksen worden de begin- en eindtekens en het teken voor een nieuwe regel bepaald.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. Gebruik de volgende variabelen om parameters toe te voegen aan de formuliergegevens:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. Maak een functie met de naam `BuildFormDataStart()` om het begin van de formuliergegevens te maken met behulp van de grenstekenreeksen en het afbeeldingspad.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. Maak een functie met de naam `BuildFormDataEnd()` om het einde van de formuliergegevens te maken met behulp van de grenstekenreeksen.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>De Bing Visual Search-API aanroepen

1. Maak een functie voor het aanroepen van het Bing Visual Search-eindpunt en het retourneren van het JSON-antwoord. In de functie zijn het begin en het einde van de formuliergegevens, een bytematrix met de afbeeldingsgegevens en een `contentType`-waarde opgenomen.

2. Gebruik een `WebRequest` voor het opslaan van de URI, contentType waarde en headers.  

3. Gebruik `request.GetRequestStream()` om uw formulier- en afbeeldingsgegevens te schrijven en vervolgens het antwoord op te halen. Uw functie moet er ongeveer uitzien als de volgende code:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>De methode Main maken

1. Haal in de methode `Main()` van uw toepassing de bestandsnaam en binaire gegevens van uw afbeelding op.

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Stel de POST-hoofdtekst in door de grens hiervoor op te maken. Roep vervolgens `BuildFormDataStart()` en `BuildFormDataEnd()` aan om de formuliergegevens te maken.

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Maak de `ContentType`-waarde door `CONTENT_TYPE_HEADER_PARAMS` en de grens van de formuliergegevens op te maken.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Haal het API-antwoord op door `BingImageSearch()` aan te roepen en het antwoord vervolgens weer te geven.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Met behulp van HttpClient

Als u `HttpClient` gebruikt, kunt u de klasse `MultipartFormDataContent` gebruiken om de formuliergegevens te maken. Gebruik de volgende secties met code om de betreffende methoden in het vorige voorbeeld te vervangen:

1. Vervang de `Main()`-methode door de volgende code:

   ```csharp
           static void Main()
           {
               try
               {
                   Console.OutputEncoding = System.Text.Encoding.UTF8;

                   if (accessKey.Length == 32)
                   {
                       if (IsImagePathSet(imagePath))
                       {
                           var filename = GetImageFileName(imagePath);
                           Console.WriteLine("Getting image insights for image: " + filename);
                           var imageBinary = GetImageBinary(imagePath);

                           var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                           var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                           Console.WriteLine("\nJSON Response:\n");
                           Console.WriteLine(JsonPrettyPrint(json));
                       }
                   }
                   else
                   {
                       Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                       Console.WriteLine("Please paste yours into the source code.");
                   }

                   Console.Write("\nPress Enter to exit ");
                   Console.ReadLine();
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   ```

2. Vervang de `BingImageSearch()`-methode door de volgende code:

   ```csharp
           /// <summary>
           /// Calls the Bing visual search endpoint and returns the JSON response.
           /// </summary>
           static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
           {
               var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
               requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

               var content = new MultipartFormDataContent(boundary);
               content.Add(new ByteArrayContent(image), "image", "myimage");
               requestMessage.Content = content;

               var httpClient = new HttpClient();

               Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
               HttpResponseMessage httpResponse = httpRequest.Result;
               HttpStatusCode statusCode = httpResponse.StatusCode;
               HttpContent responseContent = httpResponse.Content;

               string json = null;

               if (responseContent != null)
               {
                   Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                   json = stringContentsTask.Result;
               }

               return json;
           }
   ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Visual Search-web-app met één pagina maken](../tutorial-bing-visual-search-single-page-app.md)
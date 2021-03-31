---
title: 'Gebruik een voorspellingseindpunt om afbeeldingen programmatisch te testen met classificatie: Custom Vision'
titleSuffix: Azure Cognitive Services
description: Lees hoe u de API gebruikt om afbeeldingen programmatisch te testen met uw Custom Vision Service-classificatie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934749"
---
# <a name="use-your-model-with-the-prediction-api"></a>Uw model gebruiken met de Voorspellings-API

Nadat u uw model hebt getraind, kunt u afbeeldingen testen via een programma door deze te verzenden naar het API-eind punt.

> [!NOTE]
> In dit document ziet u hoe u een afbeelding bij de voorspellings-API kunt indienen met behulp van C#. Zie voor meer informatie en voor beelden de voor [Spellings-API-referentie](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Uw getrainde iteratie publiceren

Op de [Custom Vision-webpagina](https://customvision.ai) selecteert u uw project en vervolgens selecteert u het tabblad __Prestaties__.

Als u installatie kopieën wilt verzenden naar de Voorspellings-API, moet u eerst uw iteratie voor de voor spelling publiceren, die u kunt doen door __publiceren__ te selecteren en een naam op te geven voor de gepubliceerde iteratie. Hiermee wordt uw model toegankelijk gemaakt voor de Voorspellings-API van uw Custom Vision Azure-resource.

![Het tabblad prestaties wordt weer gegeven, met een rode rechthoek rondom de knop publiceren.](./media/use-prediction-api/unpublished-iteration.png)

Zodra uw model is gepubliceerd, wordt het label "gepubliceerd" weer gegeven naast uw iteratie in de zijbalk aan de linkerkant. de naam wordt weer gegeven in de beschrijving van de iteratie.

![Het tabblad prestaties wordt weer gegeven, met een rode rechthoek rondom het gepubliceerde label en de naam van de gepubliceerde iteratie.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>De URL en voorspellingssleutel ophalen

Zodra uw model is gepubliceerd, kunt u de vereiste gegevens ophalen door de URL voor de voor __Spelling__ te selecteren. Hiermee opent u een dialoog venster met informatie over het gebruik van de Voorspellings-API, met inbegrip van de __Voorspellings-URL__ en de __Voorspellings sleutel__.

![Het tabblad prestaties wordt weer gegeven met een rode rechthoek rondom de knop voor de Voorspellings-URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Het tabblad prestaties wordt weer gegeven met een rode rechthoek rondom de waarde van de Voorspellings-URL voor het gebruik van een afbeeldings bestand en de Prediction-Key waarde.](./media/use-prediction-api/prediction-api-info.png)


In deze hand leiding maakt u gebruik van een lokale installatie kopie en kopieert u de URL onder **Als u een afbeeldings bestand** op een tijdelijke locatie hebt. Kopieer ook de bijbehorende __Voorspellings sleutel__ waarde.

## <a name="create-the-application"></a>De toepassing maken

1. Maak in Visual Studio een nieuwe C#-console toepassing.

1. Gebruik de volgende code als de hoofdtekst van het bestand __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Voer de volgende informatie in:
   * Stel het `namespace` veld in op de naam van uw project.
   * Vervang de tijdelijke aanduiding door `<Your prediction key>` de sleutel waarde die u eerder hebt opgehaald.
   * Vervang de tijdelijke aanduiding door `<Your prediction URL>` de URL die u eerder hebt opgehaald.

## <a name="run-the-application"></a>De toepassing uitvoeren

Wanneer u de toepassing uitvoert, wordt u gevraagd om een pad naar een afbeeldings bestand in de-console in te voeren. De installatie kopie wordt vervolgens verzonden naar de Voorspellings-API en de Voorspellings resultaten worden geretourneerd als een JSON-indelings teken reeks. Hier volgt een voor beeld van een antwoord.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u installatie kopieën kunt verzenden naar de classificatie/detectie van uw aangepaste installatie kopie en een antwoord op een programmatische manier met de C#-SDK ontvangt. Leer vervolgens hoe u end-to-end scenario's met C# kunt volt ooien of aan de slag kunt met een andere taal-SDK.

* [Snelstartgids: Custom Vision SDK](quickstarts/image-classification.md)

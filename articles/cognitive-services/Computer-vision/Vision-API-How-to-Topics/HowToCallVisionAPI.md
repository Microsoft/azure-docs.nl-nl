---
title: De Computer Vision-API aanroepen
titleSuffix: Azure Cognitive Services
description: Lees hoe u de Computer Vision-API aanroept met behulp van de REST API in Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: abb367b64da0811a1ff46efe60b60485375f809f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486060"
---
# <a name="call-the-computer-vision-api"></a>De Computer Vision-API aanroepen

In dit artikel wordt beschreven hoe u de Computer Vision-API aanroept met behulp van de REST API. De voorbeelden zijn zowel in C# geschreven met behulp van de clientbibliotheek van de Computer Vision-API als in HTTP POST of GET-aanroepen. Dit artikel behandelt:

- Ophalen van tags, een beschrijving en categorieën
- Domeinspecifieke informatie of 'beroemdheden' ophalen

De voorbeelden in dit artikel laten de volgende functies zien:

* Het analyseren van een afbeelding om een matrix van tags en een beschrijving te retourneren
* Het analyseren van een afbeelding met een domeinspecifiek model (met name het 'beroemdhedenmodel') om het bijbehorende resultaat in JSON te retourneren

De functies bieden de volgende opties:

- **Optie 1**: Gerichte analyse: alleen een bepaald model analyseren
- **Optie 2**: Uitgebreide analyse: een analyse uitvoeren om meer informatie te geven met [86-categorietaxonomie](../Category-Taxonomy.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource </a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een afbeeldings-URL of een pad naar een lokaal opgeslagen afbeelding
* Ondersteunde invoermethoden: binaire gegevens over een onbewerkte afbeelding in de vorm van een toepassings-/octetstream of een afbeeldings-URL
* Ondersteunde bestandsindelingen voor afbeeldingen: JPEG, PNG, GIF en BMP
* Grootte van het afbeeldingsbestand: 4 MB of minder
* Afbeeldingsgrootte: 50 &times; 50 pixels of meer
  
## <a name="authorize-the-api-call"></a>De API-aanroep autoriseren

Voor elke aanroep naar de Computer Vision-API is een abonnementssleutel vereist. Deze sleutel moet worden doorgegeven via een tekenreeksparameter of worden opgegeven in de aanvraagheader.

U kunt de abonnementssleutel doorgeven op een van de volgende manieren:

* Gebruik een queryreeks, zoals in dit voorbeeld van een Computer Vision-API:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Geef deze op in de header van de HTTP-aanvraag:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Wanneer u de clientbibliotheek gebruikt, geeft u de sleutel door via de constructor van ComputerVisionClient en geeft u de regio op als eigenschap van de client:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Een installatiekopie uploaden naar de API-service van Computer Vision

De eenvoudigste manier om de Computer Vision-API-aanroep uit te voeren is door rechtstreeks een installatiekopie te uploaden om tags, een beschrijving en beroemdheden te retourneren. U doet dit door een ' POST '-aanvraag te verzenden met de binaire installatiekopie in de HTTP-hoofdtekst, samen met de gegevens uit de installatiekopie. De uploadmethode is hetzelfde voor alle Computer Vision-API-aanroepen. Het enige verschil zijn de queryparameters die u opgeeft. 

Haal voor een bepaalde installatiekopie tags en een beschrijving op met een van de volgende opties:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Optie 1: Een lijst met tags en een beschrijving ophalen

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Optie 2: Enkel een lijst met tags of een beschrijving ophalen

Om enkel tags op te halen, voert u uit:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Om enkel een beschrijving op te halen, voert u uit:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Domeinspecifieke analyse ophalen (beroemdheden)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Optie 1: Gerichte analyse: alleen een bepaald model analyseren
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Voor deze optie zijn alle andere queryparameters {visualFeatures, details} niet geldig. Als u alle ondersteunde modellen wilt zien, gebruikt u:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Optie 2: Uitgebreide analyse: een analyse uitvoeren om meer informatie te geven met 86-categorietaxonomie

Voor toepassingen waarbij u een generieke afbeeldingsanalyse wilt ophalen naast details uit één of meer domeinspecifieke modellen, breidt u de v1-API uit met behulp van de queryparameter voor modellen.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Wanneer u deze methode aanroept, roept u eerst de classificatie van de [86-categorie](../Category-Taxonomy.md) aan. Als een van de categorieën overeenkomt met de categorie van een bekend of overeenkomstig model, treedt een tweede ronde van classificatie-aanroepen op. Als 'details=all' of 'details' bijvoorbeeld 'beroemdheden' bevat, dan kunt u het model beroemdheden aanroepen nadat u de classificatie van de 86-categorie heeft aangeroepen. Het resultaat bevat de categorie persoon. In tegenstelling tot optie 1 verhoogt deze methode de latentie voor gebruikers die geïnteresseerd zijn in beroemdheden.

Alle v1-queryparameters gedragen zich in dit geval op dezelfde manier. Als u geen visualFeatures=categories opgeeft, wordt dit impliciet ingeschakeld.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>De JSON-uitvoer ophalen en begrijpen voor analyse

Hier volgt een voorbeeld:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Veld | Type | Inhoud
------|------|------|
Tags  | `object` | Het object op het hoogste niveau voor een matrix van tags.
tags[].Name | `string`    | Het trefwoord uit de classificatie van tags.
tags[].Score    | `number`    | De betrouwbaarheidsscore, tussen 0 en 1.
description     | `object`    | Het object op het hoogste niveau voor een beschrijving.
description.tags[] |    `string`    | De lijst met tags.  Als de mogelijkheid om een bijschrift te produceren niet betrouwbaar genoeg is, zijn de tags mogelijk de enige informatiebron die voor de aanroepende functie beschikbaar is.
description.captions[].text    | `string`    | Een zin die de afbeelding beschrijft.
description.captions[].confidence    | `number`    | De betrouwbaarheidsscore voor de zin.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>De JSON-uitvoer van domeinspecifieke modellen ophalen en begrijpen

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Optie 1: Gerichte analyse: alleen een bepaald model analyseren

De uitvoer is een matrix van tags, zoals te zien in het volgende voorbeeld:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Optie 2: Uitgebreide analyse: een analyse uitvoeren om meer informatie te geven met '86-categorietaxonomie'

Voor domeinspecifieke modellen die Optie 2 (uitgebreide analyse) gebruiken, wordt het retourtype voor categorieën uitgebreid, zoals te zien in het volgende voorbeeld:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Het categorieënveld is een lijst met één of meer van de [86-categorieën](../Category-Taxonomy.md) in de oorspronkelijke taxonomie. Categorieën die op een onderstrepingsteken eindigen, komen overeen met die categorie en de bijbehorende onderliggende elementen (bijvoorbeeld 'personen_' of 'personen_groep', voor het beroemdhedenmodel).

Veld    | Type    | Inhoud
------|------|------|
categorieën | `object`    | Het object op het hoogste niveau.
categories[].name     | `string`    | De naam uit de lijst van de 86-categorietaxonomie.
categories[].score    | `number`    | De betrouwbaarheidsscore, tussen 0 en 1.
categories[].detail     | `object?`      | (Optioneel) Het detailobject.

Als meerdere categorieën overeenkomen (de 86-categorieënclassificatie retourneert bijvoorbeeld een score voor zowel 'personen_' als 'personen_jong' wanneer model=beroemdheden), worden de details gekoppeld aan de overeenkomst op het meest algemene niveau (in dit voorbeeld 'personen_').

## <a name="error-responses"></a>Foutreactiess

Deze fouten zijn identiek aan die in vision.analyze, met de aanvullende NotSupportedModel-fout (HTTP 400), die mogelijk wordt geretourneerd in scenario's voor zowel Optie 1 als Optie 2. Als voor Optie 2 (uitgebreide analyse) eender welke van de modellen die bij de details zijn opgegeven niet worden herkend, dan retourneert de API een NotSupportedModel, zelfs als één of meer geldig zijn. Om te achterhalen welke modellen worden ondersteund kunt u listModels aanroepen.

## <a name="next-steps"></a>Volgende stappen

Als u de REST-API wilt gebruiken, gaat u naar [Computer Vision-API-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).

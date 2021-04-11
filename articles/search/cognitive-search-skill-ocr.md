---
title: OCR cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Extraheer tekst uit afbeeldings bestanden met behulp van optische teken herkenning (OCR) in een verrijkings pijplijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: aa9c8e1d5579538df11358edc08eb7e2043cea74
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285838"
---
# <a name="ocr-cognitive-skill"></a>OCR cognitieve vaardigheid

De vaardigheid van **optische teken herkenning (OCR)** herkent gedrukte en handgeschreven tekst in afbeeldings bestanden. Deze vaardigheid maakt gebruik van de machine learning modellen die door [Computer Vision](../cognitive-services/computer-vision/overview.md) API [v 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) in cognitive services worden verschaft. De **OCR** -vaardigheid is toegewezen aan de volgende functionaliteit:

+ Voor Engels, Spaans, Duits, Frans, Italiaans, Portugees en Nederlands wordt de nieuwe [Lees](../cognitive-services/computer-vision/overview-ocr.md#read-api) -API gebruikt.
+ Voor alle andere talen wordt de [verouderde OCR](../cognitive-services/computer-vision/overview-ocr.md#ocr-api) -API gebruikt.

De **OCR** -vaardigheid extraheert tekst uit afbeeldings bestanden. Ondersteunde bestands indelingen zijn onder andere:

+ . INDELING
+ . JPG
+ . PNG
+ . BITMAP
+ . AFBEELDING
+ . RELEVANTE

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).


## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| `detectOrientation`    | Hiermee wordt automatische detectie van de afbeeldings stand ingeschakeld. <br/> Geldige waarden: True/False.|
| `defaultLanguageCode` | <p>    De taal code van de invoer tekst. Enkele ondersteunde talen: <br/> zh-Hans (ChineseSimplified) <br/> zh-hant (ChineseTraditional) <br/>CS (Tsjechisch) <br/>da (Deens) <br/>nl (Nederlands) <br/>nl (Engels) <br/>fi (Fins)  <br/>FR (Frans) <br/>  de (Duits) <br/>El (Grieks) <br/> hu (Hong aars) <br/> It (Italiaans) <br/>  Ja (Japans) <br/> ko (Koreaans) <br/> NB (Noors) <br/>   pl (Pools) <br/> PT (Portugees) <br/>  ru (Russisch) <br/>  ES (Spaans) <br/>  SV (Zweeds) <br/>  tr (Turks) <br/> AR (Arabisch) <br/> ro (Roemeens) <br/> SR-Cyrl (SerbianCyrillic) <br/> sr-Latn (SerbianLatin) <br/>  SK (Slowaaks) <br/>  UNK (onbekend) <br/><br/> Als de taal code niet is opgegeven of null is, wordt de taal ingesteld op Engels. Als de taal expliciet is ingesteld op ' UNK ', wordt de taal automatisch gedetecteerd. </p> |
| `lineEnding` | De waarde die moet worden gebruikt tussen elke gedetecteerde regel. Mogelijke waarden: ' spatie ', ' CarriageReturn ', ' regelinvoerteken '.  De standaard waarde is ' spatie '. |

Voorheen was er een para meter met de naam ' textExtractionAlgorithm ' om aan te geven of de vaardigheid "gedrukte" of "handgeschreven" tekst moet extra heren.  Deze para meter is afgeschaft en niet langer nodig als de meest recente Lees-API-algoritme kan beide typen tekst tegelijk ophalen.  Als uw vaardigheids definitie deze para meter al bevat, hoeft u deze niet te verwijderen, maar deze wordt niet meer gebruikt en beide typen tekst worden uitgepakt, ongeacht waar deze is ingesteld.

## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam      | Beschrijving                                          |
|---------------|------------------------------------------------------|
| `image`         | Complex type. Momenteel werkt alleen met het veld '/document/normalized_images ', dat door de indexer van Azure Blob ```imageAction``` is geproduceerd wanneer is ingesteld op een andere waarde dan ```none``` . Zie het voor [beeld](#sample-output) voor meer informatie.|


## <a name="skill-outputs"></a>Vaardigheids uitvoer
| Uitvoer naam      | Beschrijving                   |
|---------------|-------------------------------|
| `text`             | Tekst zonder opmaak opgehaald uit de afbeelding.   |
| `layoutText`    | Complex type waarmee de geëxtraheerde tekst wordt beschreven en de locatie waar de tekst is gevonden.|


## <a name="sample-definition"></a>Voorbeeld definitie

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Voorbeeld tekst en layoutText uitvoer

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Voor beeld: tekst die is geëxtraheerd uit Inge sloten afbeeldingen samen voegen met de inhoud van het document.

Een veelgebruikte use-case voor tekst fusie is de mogelijkheid om de tekstuele weer gave van afbeeldingen (tekst van een OCR-vaardigheid of het bijschrift van een afbeelding) samen te voegen in het veld inhoud van een document.

In het volgende voor beeld wordt een *merged_text* veld gemaakt. Dit veld bevat de tekstuele inhoud van uw document en de OCRed tekst van elk van de afbeeldingen die in dat document zijn Inge sloten.

#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text",
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
In het bovenstaande voor beeld van de vaardig heden wordt ervan uitgegaan dat er een veld met genormaliseerde afbeeldingen bestaat. Als u dit veld wilt genereren, stelt u de *imageAction* -configuratie in de definitie van de Indexeer functie in op *generateNormalizedImages* zoals hieronder wordt weer gegeven:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zie ook
+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [TextMerger-vaardigheid](cognitive-search-skill-textmerger.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Indexeerfunctie maken (REST)](/rest/api/searchservice/create-indexer)
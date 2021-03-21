---
title: Herkennings vaardigheid van taal detectie
titleSuffix: Azure Cognitive Search
description: Evalueert ongestructureerde tekst en retourneert voor elke record een taal-id met een score die de sterkte van de analyse in een AI-verrijkings pijplijn in azure Cognitive Search aangeeft.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548050"
---
#   <a name="language-detection-cognitive-skill"></a>Herkennings vaardigheid van taal detectie

De **taaldetectie** vaardigheid detecteert de taal van invoer tekst en rapporteert één taal code voor elk document dat voor de aanvraag wordt verzonden. De taal code is gekoppeld aan een score die de sterkte van de analyse aangeeft. Deze vaardigheid maakt gebruik van de machine learning modellen van [Text Analytics](../cognitive-services/text-analytics/overview.md) in cognitive Services.

Deze mogelijkheid is vooral nuttig wanneer u de taal van de tekst wilt opgeven als invoer voor andere vaardig heden (bijvoorbeeld de kwalificatie [sentimentanalyse vaardigheid](cognitive-search-skill-sentiment.md) of [tekst splitsen](cognitive-search-skill-textsplit.md)).

Met taal detectie kunt u gebruikmaken van de natuurlijke taal verwerkings bibliotheken van Bing, die het aantal [ondersteunde talen en regio's](../cognitive-services/text-analytics/language-support.md) overschrijdt dat voor Text Analytics wordt vermeld. De exacte lijst met talen wordt niet gepubliceerd, maar bevat alle talen met veel spraak, plus varianten, dialecten en enkele regionale en culturele talen. Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u [de taaldetectie-API proberen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd is `(Unknown)` .

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](/dotnet/api/system.string.length) . Als u uw gegevens wilt opsplitsen voordat u deze naar de taal detectie vaardigheid verzendt, kunt u de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Invoerwaarden | Beschrijving |
|---------------------|-------------|
| `defaultCountryHint` | Beschrijving Een ISO 3166-1 alpha-2 2 letter land code kan worden gebruikt als hint voor het taal detectie model als de taal niet kan worden dubbel zinnigheid. Zie [de documentatie over Text Analytics](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) in dit onderwerp voor meer informatie. Met name de `defaultCountryHint` para meter wordt gebruikt met documenten die de `countryHint` invoer niet expliciet opgeven.  |
| `modelVersion`   | Beschrijving De versie van het model dat moet worden gebruikt bij het aanroepen van de Text Analytics service. Het is standaard de meest recente versie die beschikbaar is wanneer deze niet is opgegeven. We raden u aan deze waarde alleen op te geven als dit absoluut nood zakelijk is. Zie [model versie beheer in de Text Analytics-API](../cognitive-services/text-analytics/concepts/model-versioning.md) voor meer informatie. |

## <a name="skill-inputs"></a>Vaardigheids invoer

Parameters zijn hoofdlettergevoelig.

| Invoerwaarden     | Beschrijving |
|--------------------|-------------|
| `text` | De tekst die moet worden geanalyseerd.|
| `countryHint` | Een ISO 3166-1 alpha-2 2 letter land code die als hint voor het taal detectie model moet worden gebruikt als de taal niet kan worden dubbel zinnigheid. Zie [de documentatie over Text Analytics](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) in dit onderwerp voor meer informatie. |

## <a name="skill-outputs"></a>Vaardigheids uitvoer

| Uitvoer naam    | Beschrijving |
|--------------------|-------------|
| `languageCode` | De ISO 6391-taal code voor de geïdentificeerde taal. Bijvoorbeeld ' en '. |
| `languageName` | De naam van de taal. Bijvoorbeeld ' Engels '. |
| `score` | Een waarde tussen 0 en 1. De kans dat de taal correct wordt geïdentificeerd. De score kan lager zijn dan 1 als de zin gemengde talen heeft.  |

##  <a name="sample-definition"></a>Voorbeeld definitie

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
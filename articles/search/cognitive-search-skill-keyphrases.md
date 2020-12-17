---
title: Sleuteltermextractie cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Hiermee wordt ongestructureerde tekst geëvalueerd en voor elke record een lijst met sleutel zinnen in een AI-pijp lijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2fdebffbf9643febc08cba997b3a5a5fc4bb5998
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652310"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Sleuteltermextractie cognitieve vaardigheid

Met de **Sleuteltermextractie** vaardigheid wordt ongestructureerde tekst geëvalueerd, en voor elke record wordt een lijst met sleutel zinnen geretourneerd. Deze vaardigheid maakt gebruik van de machine learning modellen van [Text Analytics](../cognitive-services/text-analytics/overview.md) in cognitive Services.

Deze mogelijkheid is handig als u snel de belangrijkste pratende punten in de record moet identificeren. Als er bijvoorbeeld een invoer tekst is, is de levens middelen Delicious en zijn er fantastische mede werkers. de service retourneert "voedsel" en "fantastische mede werkers".

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](/dotnet/api/system.string.length) . Als u uw gegevens moet opsplitsen voordat u deze naar de sleutel woord groep verstuurt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Invoer | Beschrijving |
|---------------------|-------------|
| `defaultLanguageCode` | Beschrijving De taal code die moet worden toegepast op documenten die geen taal expliciet opgeven.  Als de standaard taal code niet wordt opgegeven, wordt Engels (en) gebruikt als de standaard taal code. <br/> Bekijk de [volledige lijst met ondersteunde talen](../cognitive-services/text-analytics/language-support.md). |
| `maxKeyPhraseCount`   | Beschrijving Het maximum aantal sleutel zinnen dat moet worden geproduceerd. |

## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer  | Beschrijving |
|--------------------|-------------|
| `text` | De tekst die moet worden geanalyseerd.|
| `languageCode`    |  Een teken reeks die de taal van de records aangeeft. Als deze para meter niet wordt opgegeven, wordt de standaardtaal code gebruikt voor het analyseren van de records. <br/>[Volledige lijst met ondersteunde talen](../cognitive-services/text-analytics/language-support.md) weer geven|

## <a name="skill-outputs"></a>Vaardigheids uitvoer

| Uitvoer     | Beschrijving |
|--------------------|-------------|
| `keyPhrases` | Een lijst met tref woorden die zijn geëxtraheerd uit de invoer tekst. De sleutel zinnen worden geretourneerd op volg orde van belang. |


##  <a name="sample-definition"></a>Voorbeeld definitie

Overweeg een SQL-record met de volgende velden:

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Uw vaardigheids definitie kan er als volgt uitzien:

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>Voorbeelduitvoer

Voor het bovenstaande voor beeld wordt de uitvoer van uw vaardigheid geschreven naar een nieuw knoop punt in de gerijkte structuur ' document-myKeyPhrases ', omdat dat is `targetName` opgegeven. Als u geen opgeeft `targetName` , dan zou het ' document/woordgroepen ' zijn.

#### <a name="documentmykeyphrases"></a>document-myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

U kunt ' document-myKeyPhrases ' gebruiken als invoer voor andere vaardig heden of als bron van een [uitvoer veld toewijzing](cognitive-search-output-field-mapping.md).

## <a name="errors-and-warnings"></a>Fouten en waarschuwingen
Als u een niet-ondersteunde taal code opgeeft, wordt er een fout gegenereerd en worden er geen sleutel zinnen geëxtraheerd.
Als uw tekst leeg is, wordt er een waarschuwing gegenereerd.
Als uw tekst groter is dan 50.000 tekens, worden alleen de eerste 50.000 tekens geanalyseerd en wordt er een waarschuwing gegeven.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Hoe kan ik uitvoer veld toewijzingen definiëren?](cognitive-search-output-field-mapping.md)
---
title: Herkennings vaardigheid van entity erkennen
titleSuffix: Azure Cognitive Search
description: Extraheer verschillende typen entiteiten uit tekst in een verrijkings pijplijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3300d68c6356cb7cdd56a9ad79eb0ac011942eb2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547897"
---
#    <a name="entity-recognition-cognitive-skill"></a>Herkennings vaardigheid van entity erkennen

De kwalificatie voor **entiteits herkenning** extraheert entiteiten van verschillende typen van tekst. Deze vaardigheid maakt gebruik van de machine learning modellen van [Text Analytics](../cognitive-services/text-analytics/overview.md) in cognitive Services.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. EntityRecognitionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](/dotnet/api/system.string.length) . Als u uw gegevens moet opsplitsen voordat u deze naar de sleutel woord groep verstuurt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Para meters zijn hoofdletter gevoelig en zijn allemaal optioneel.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| `categories`    | Matrix van categorieën die moeten worden geëxtraheerd.  Mogelijke categorie typen: `"Person"` , `"Location"` , `"Organization"` , `"Quantity"` , `"Datetime"` , `"URL"` , `"Email"` . Als er geen categorie wordt opgegeven, worden alle typen geretourneerd.|
| `defaultLanguageCode` |    De taal code van de invoer tekst. De volgende talen worden ondersteund: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` . Niet alle entiteits categorieën worden ondersteund voor alle talen. Zie de opmerking hieronder.|
| `minimumPrecision` | Een waarde tussen 0 en 1. Als de betrouwbaarheids Score (in de `namedEntities` uitvoer) lager is dan deze waarde, wordt de entiteit niet geretourneerd. De standaardwaarde is 0. |
| `includeTypelessEntities` | Stel `true` deze in als u bekende entiteiten wilt herkennen die niet aan de huidige categorieën voldoen. In het complexe uitvoer veld worden herkende entiteiten geretourneerd `entities` . "Windows 10" is bijvoorbeeld een bekende entiteit (een product), maar omdat "Products" geen ondersteunde categorie is, wordt deze entiteit opgenomen in het uitvoer veld entiteiten. Standaard waarde is `false` |


## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam      | Beschrijving                   |
|---------------|-------------------------------|
| `languageCode`    | Optioneel. De standaardinstelling is `"en"`.  |
| `text`          | De tekst die moet worden geanalyseerd.          |

## <a name="skill-outputs"></a>Vaardigheids uitvoer

> [!NOTE]
> Niet alle entiteits categorieën worden ondersteund voor alle talen. De `"Person"` `"Location"` categorie typen,, en `"Organization"` entiteit worden ondersteund voor de volledige lijst met talen hierboven. Alleen _de_ modules _, en,_ _es_, _fr_ en _zh-Hans_ ondersteunen extra heren,,, `"Quantity"` `"Datetime"` `"URL"` en `"Email"` typen. Zie [taal-en regio ondersteuning voor de Text Analytics-API](../cognitive-services/text-analytics/language-support.md)voor meer informatie.  

| Uitvoer naam      | Beschrijving                   |
|---------------|-------------------------------|
| `persons`       | Een matrix met teken reeksen waarbij elke teken reeks de naam van een persoon vertegenwoordigt. |
| `locations`  | Een matrix met teken reeksen waarbij elke teken reeks een locatie vertegenwoordigt. |
| `organizations`  | Een matrix met teken reeksen waarbij elke teken reeks een organisatie vertegenwoordigt. |
| `quantities`  | Een matrix met teken reeksen waarbij elke teken reeks een hoeveelheid vertegenwoordigt. |
| `dateTimes`  | Een matrix met teken reeksen waarbij elke teken reeks een datum/tijd vertegenwoordigt (zoals deze in de tekst) waarde wordt weer gegeven. |
| `urls` | Een matrix met teken reeksen waarbij elke teken reeks een URL vertegenwoordigt |
| `emails` | Een matrix met teken reeksen waarbij elke teken reeks een e-mail vertegenwoordigt |
| `namedEntities` | Een matrix met complexe typen die de volgende velden bevat: <ul><li>category</li> <li>waarde (de werkelijke naam van de entiteit)</li><li>offset (de locatie waar deze zich bevindt in de tekst)</li><li>vertrouwen (hogere waarde betekent dat het meer een echte entiteit is)</li></ul> |
| `entities` | Een matrix met complexe typen met uitgebreide informatie over de entiteiten die zijn geëxtraheerd uit de tekst, met de volgende velden <ul><li> naam (de werkelijke naam van de entiteit. Dit vertegenwoordigt een ' genormaliseerd ' formulier.</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (een koppeling naar de Wikipedia-pagina voor de entiteit)</li><li>bingId</li><li>type (de categorie van de entiteit die wordt herkend)</li><li>subtype (alleen beschikbaar voor bepaalde categorieën geeft dit een gedetailleerdere weer gave van het entiteits type)</li><li> komt overeen (een complexe verzameling die bevat)<ul><li>tekst (de onbewerkte tekst voor de entiteit)</li><li>offset (de locatie waar deze zich bevindt)</li><li>lengte (de lengte van de tekst van de onbewerkte entiteit)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Voorbeeld definitie

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Houd er rekening mee dat de verplaatsingen die worden geretourneerd voor entiteiten in de uitvoer van deze vaardigheid rechtstreeks worden geretourneerd uit de [Text Analytics-API](../cognitive-services/text-analytics/overview.md). Dit betekent dat als u deze gebruikt om te indexeren in de oorspronkelijke teken reeks, u de [StringInfo](/dotnet/api/system.globalization.stringinfo) -klasse in .net moet gebruiken om de juiste inhoud te extra heren.  [Meer informatie vindt u hier.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Waarschuwings cases
Als de taal code voor het document niet wordt ondersteund, wordt een waarschuwing geretourneerd en worden er geen entiteiten geëxtraheerd.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
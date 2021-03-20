---
title: Cognitieve vaardigheid van shaper
titleSuffix: Azure Cognitive Search
description: Haal meta gegevens en gestructureerde informatie op uit ongestructureerde gegevens en vorm deze als een complex type in een AI-verrijkings pijplijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 81eb0e60befc544a6c3bee8f04e901b6a5e472bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85560823"
---
# <a name="shaper-cognitive-skill"></a>Cognitieve vaardigheid van shaper

De **shaper** -vaardigheid consolideert diverse invoer in een [complex type](search-howto-complex-data-types.md) waarnaar later in de verrijkings pijplijn kan worden verwezen. Met de **shaper** -vaardigheid kunt u in wezen een structuur maken, de naam van de leden van die structuur definiëren en waarden aan elk lid toewijzen. Voor beelden van geconsolideerde velden die handig zijn in zoek scenario's zijn onder andere het combi neren van een voor-en achternaam in één structuur, plaats en provincie in één structuur, of naam en geboorte datum in één structuur om unieke identiteiten te bepalen.

Daarnaast voegt de **shaper** -vaardigheid die in [scenario 3](#nested-complex-types) wordt geïllustreerd, een optionele eigenschap *sourceContext* toe aan de invoer. De *bron* -en *sourceContext* -eigenschappen sluiten elkaar wederzijds uit. Als de invoer zich in de context van de vaardigheid bevindt, hoeft u alleen *bron* te gebruiken. Als de invoer zich in een *andere* context bevindt dan de context van de kwalificatie, gebruikt u de *sourceContext*. De *sourceContext* vereist dat u een geneste invoer definieert met het specifieke element dat als bron wordt behandeld. 

De uitvoer naam is altijd "uitvoer". Intern kan de pijp lijn een andere naam toewijzen, zoals ' analyzedText ', zoals wordt weer gegeven in de onderstaande voor beelden, maar de **shaper** -vaardigheid zelf retourneert ' output ' in het antwoord. Dit kan belang rijk zijn als u fouten opspoort in verrijkte documenten en u merkt dat de naam verschilt, of als u een aangepaste vaardigheid bouwt en het antwoord zelf structureert.

> [!NOTE]
> De **shaper** -vaardigheid is niet gebonden aan een COGNITIVE Services-API en er worden geen kosten in rekening gebracht voor het gebruik ervan. U moet nog steeds [een Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md)om de optie **gratis** resource te overschrijven, waardoor u een klein aantal dagelijkse verrijkingen per dag beperkt.

## <a name="odatatype"></a>@odata.type  
Micro soft. skills. util. ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: complexe typen

Overweeg een scenario waarin u een structuur wilt maken met de naam *analyzedText* die twee leden bevat: respectievelijk *Text* en *sentiment*. In een index wordt een doorzoekbaar veld dat uit meerdere delen bestaat een *complex type* genoemd. het wordt vaak gemaakt wanneer bron gegevens een overeenkomende complexe structuur hebben die hieraan wordt toegewezen.

Een andere benadering voor het maken van complexe typen is echter via de **shaper** -vaardigheid. Door deze vaardigheid op te nemen in een vaardig heden, kunnen de in-Memory bewerkingen tijdens de kwalificatieset-verwerking gegevensshapes uitvoeren met geneste structuren, die vervolgens kunnen worden toegewezen aan een complex type in uw index. 

In het volgende voor beeld van de vaardigheids definitie worden de lidnamen als invoer opgegeven. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Voor beeld-index

Een vaardig heden wordt aangeroepen door een Indexeer functie en voor een Indexeer functie is een index vereist. Een weer gave met complexe velden in uw index kan eruitzien als in het volgende voor beeld. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Vaardigheids invoer

Een binnenkomend JSON-document dat bruikbare invoer biedt voor deze **shaper** -vaardigheid kan het volgende zijn:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Vaardigheids uitvoer

De **shaper** -vaardigheid genereert een nieuw element met de naam *analyzedText* met de gecombineerde elementen van *Text* en *sentiment*. Deze uitvoer voldoet aan het index schema. Het wordt geïmporteerd en geïndexeerd in een Azure Cognitive Search-index.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scenario 2: invoer consolidatie

Een ander voor beeld: Stel dat u in verschillende stadia van pijplijn verwerking de titel van een boek hebt geëxtraheerd en hoofdstuk titels op verschillende pagina's van het boek hebt. U kunt nu één structuur maken die bestaat uit de volgende verschillende invoer.

De **shaper** -vaardigheids definitie voor dit scenario kan er als volgt uitzien:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Vaardigheids uitvoer
In dit geval worden alle hoofdstuk titels door de **shaper** afgevlakt om één matrix te maken. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: invoer consolidatie vanuit geneste contexten

Stel dat u de titel, de hoofd stukken en de inhoud van een boek hebt en dat u entiteits herkenning en sleutel zinnen op de inhoud hebt uitgevoerd en nu de resultaten van de verschillende vaardig heden in één vorm kunt samen voegen met de hoofdstuk naam, entiteiten en sleutel zinnen.

De **shaper** -vaardigheids definitie voor dit scenario kan er als volgt uitzien:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Vaardigheids uitvoer
In dit geval maakt de **shaper** een complex type. Deze structuur bevindt zich in het geheugen. Als u het wilt opslaan in een [Knowledge Store](knowledge-store-concept-intro.md), moet u een projectie maken in uw vaardig heden die opslag kenmerken definieert.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Complexe typen gebruiken](search-howto-complex-data-types.md)
+ [Kennisarchief](knowledge-store-concept-intro.md)
+ [Een kennis archief maken in REST](knowledge-store-create-rest.md)
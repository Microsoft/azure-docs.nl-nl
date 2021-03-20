---
title: Cognitieve vaardigheid van Image Analysis
titleSuffix: Azure Cognitive Search
description: Extraheer semantische tekst via afbeeldings analyse met behulp van de functie voor het analyseren van de afbeeldings analyse in een AI-pijp lijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 69b84a3edb606ed99b6aaca7db5ad0e57124f1b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948932"
---
# <a name="image-analysis-cognitive-skill"></a>Cognitieve vaardigheid van Image Analysis

De kwalificatie **analyse van installatie kopieën** extraheert een uitgebreide set visuele functies op basis van de inhoud van de installatie kopie. U kunt bijvoorbeeld een bijschrift genereren op basis van een afbeelding, tags genereren of beroemdheden en bezienswaardigheden identificeren. Deze vaardigheid maakt gebruik van de machine learning modellen van [Computer Vision](../cognitive-services/computer-vision/overview.md) in cognitive Services. 

> [!NOTE]
> Kleine volumes (minder dan 20 trans acties) kunnen gratis worden uitgevoerd in azure Cognitive Search, maar voor grotere werk belastingen moet [een factureer bare Cognitive Services resource worden gekoppeld](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Vision. ImageAnalysisSkill 

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| `defaultLanguageCode` |  Een teken reeks die aangeeft welke taal moet worden geretourneerd. De service retourneert herkennings resultaten in een opgegeven taal. Als deze para meter niet wordt opgegeven, is de standaard waarde "en". <br/><br/>Ondersteunde talen zijn: <br/>*en* -Engels (standaard) <br/> *es* -Spaans <br/> *Ja* , Japans <br/> *PT* -Portugees <br/> *zh* -vereenvoudigd Chinees|
| `visualFeatures` |    Een matrix met teken reeksen die aangeeft welke Visual-functie typen moeten worden geretourneerd. Geldige typen visuele functies zijn:  <ul><li>*volwassene* : detecteert of de installatie kopie een porno grafie heeft (komt voor een naaktheid of een geslachte handeling) of is benchmarks (voor beeld van extreem geweld of bloed). Er wordt ook een expliciete suggestie voor inhoud (ook wel ongepaste-inhoud genoemd) gedetecteerd.</li><li>*Brands* : detecteert verschillende merken in een installatie kopie, met inbegrip van de geschatte locatie. De visuele functie *Brands* is alleen beschikbaar in het Engels.</li><li> *Categorieën* : de afbeeldings inhoud wordt gecategoriseerd op basis van een taxonomie die is gedefinieerd in de Cognitive Services [Computer Vision documentatie](../cognitive-services/computer-vision/category-taxonomy.md). </li><li>*Beschrijving* : Hiermee wordt de afbeeldings inhoud met een volledige zin in ondersteunde talen beschreven.</li><li>*gezichten* : detecteert of er gezichten aanwezig zijn. Indien aanwezig, worden coördinaten, geslacht en leeftijd gegenereerd.</li><li>  *objecten* : detecteert verschillende objecten in een installatie kopie, met inbegrip van de geschatte locatie. De Visual-functie voor *objecten* is alleen beschikbaar in het Engels.</li><li> *Tags* : Tags de afbeelding met een gedetailleerde lijst met woorden die betrekking hebben op de inhoud van de installatie kopie.</li></ul> Namen van visuele functies zijn hoofdletter gevoelig. Houd er rekening mee dat de visuele functies *kleur* en *imageType* zijn afgeschaft, maar deze functionaliteit kan nog steeds worden geopend via een [aangepaste vaardigheid](./cognitive-search-custom-skill-interface.md).|
| `details` | Een matrix met teken reeksen die aangeeft welke specifieke details van het domein moeten worden geretourneerd. Geldige typen visuele functies zijn: <ul><li>*beroemdheden* : identificeert beroemdheden als deze wordt gedetecteerd in de installatie kopie.</li><li>*bezienswaardigheden* : identificeert bezienswaardigheden als deze worden gedetecteerd in de installatie kopie. </li></ul> |

## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam      | Beschrijving                                          |
|---------------|------------------------------------------------------|
| `image`         | Complex type. Momenteel werkt alleen met het veld '/document/normalized_images ', dat door de indexer van Azure Blob ```imageAction``` is geproduceerd wanneer is ingesteld op een andere waarde dan ```none``` . Zie het voor [beeld](#sample-output) voor meer informatie.|



##  <a name="sample-skill-definition"></a>Voor beeld van vaardigheids definitie

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Voor beeld van index (alleen voor de velden categorieën, beschrijving, gezichten en Tags)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Voorbeeld toewijzing van uitvoer velden (voor de bovenstaande index)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Variatie op uitvoer veld toewijzingen (geneste eigenschappen)

U kunt uitvoer veld toewijzingen definiëren voor eigenschappen op lagere niveaus, zoals alleen bezienswaardigheden of beroemdheden. Zorg er in dit geval voor dat het index schema een veld heeft dat de specifieke bezienswaardigheden bevat.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Fout cases
In de volgende gevallen worden er geen elementen geëxtraheerd.

| Foutcode | Beschrijving |
|------------|-------------|
| `NotSupportedLanguage` | De gegeven taal wordt niet ondersteund. |
| `InvalidImageUrl` | De afbeeldings-URL heeft een ongeldige indeling of is niet toegankelijk.|
| `InvalidImageFormat` | De invoer gegevens zijn geen geldige installatie kopie. |
| `InvalidImageSize` | De invoer afbeelding is te groot. |
| `NotSupportedVisualFeature`  | Het opgegeven functie type is niet geldig. |
| `NotSupportedImage` | Niet-ondersteunde installatie kopie, bijvoorbeeld onderliggende porno grafie. |
| `InvalidDetails` | Niet-ondersteund domein-specifiek model. |

Als u de fout melding ziet `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"` , controleert u het pad. Zowel beroemdheden als bezienswaardigheden zijn eigenschappen `detail` .

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Indexeerfunctie maken (REST)](/rest/api/searchservice/create-indexer)
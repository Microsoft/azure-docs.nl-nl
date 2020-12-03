---
title: Vaardig heden en werk stroom van vakkennisset
titleSuffix: Azure Cognitive Search
description: Met vaardig heden kunt u een AI-verrijkings pijplijn ontwerpen in azure Cognitive Search. Meer informatie over belang rijke concepten en informatie over het samen stellen van vaardig heden.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558110"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>De concepten van vaardig heden in azure Cognitive Search

Dit artikel is voor ontwikkel aars die meer inzicht hebben in de concepten en samen stelling van vaardig heden en wordt ervan uitgegaan dat u bekend bent met het proces van AI-verrijking. Als u geen ervaring hebt met dit concept, begint u met [AI-verrijking in Azure Cognitive Search](cognitive-search-concept-intro.md).

## <a name="introducing-skillsets"></a>Inleiding tot vaardig heden

Een vaardig heden is een herbruikbare resource in azure Cognitive Search die aan een Indexeer functie is gekoppeld en geeft een verzameling vaardig heden op die worden gebruikt voor het analyseren, transformeren en verrijken van de inhoud van tekst of afbeeldingen tijdens het indexeren. Vaardig heden hebben invoer en uitvoer, en vaak wordt de uitvoer van de ene vaardigheid de invoer van een andere in een keten of reeks processen.

Een vakkennisset heeft drie hoofd eigenschappen:

+ `skills`, een niet-geordende verzameling vaardig heden waarvoor het platform de volg orde van de uitvoering bepaalt op basis van de vereiste invoer voor elke vaardigheid.
+ `cognitiveServices`is dit de sleutel van een Cognitive Services resource die afbeeldingen en tekst verwerking uitvoert voor vaardig heden die ingebouwde vaardig heden bevatten.
+ `knowledgeStore`(optioneel) een Azure Storage-account waarin uw verrijkte documenten worden geprojecteerd. Verrijkte documenten worden ook gebruikt door zoek indexen.

Vaardig heden zijn geschreven in JSON. Het volgende voor beeld is een enigszins vereenvoudigde versie van dit [Hotel: beoordelingen vaardig heden](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json), die worden gebruikt voor het illustreren van concepten in dit artikel. 

De eerste twee vaardig heden worden hieronder weer gegeven:

+ Vaardigheids #1 is een [kwalificatie voor tekst splitsing](cognitive-search-skill-textsplit.md) waarmee de inhoud van het veld reviews_text als invoer wordt geaccepteerd en die inhoud wordt gesplitst in ' pagina's ' van 5000 tekens als uitvoer.
+ Vaardigheids #2 is ' pagina's ' die door de [sentiment worden gedetecteerd](cognitive-search-skill-sentiment.md) als invoer en er wordt een nieuw veld met de naam ' sentiment ' gegenereerd als uitvoer met de resultaten van de analyse van sentiment.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> U kunt complexe vaardig heden maken met lussen en vertakkingen, met behulp van de [voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md) om de expressies te maken. De syntaxis is gebaseerd op de notatie van het pad naar de [JSON-pointer](https://tools.ietf.org/html/rfc6901) , met een paar wijzigingen in het identificeren van knoop punten in de verrijkings structuur. Er wordt `"/"` een niveau lager in de structuur door lopen en  `"*"` fungeert als een for-each-operator in de context. Een groot aantal voor beelden in dit artikel illustreren de syntaxis. 

### <a name="enrichment-tree"></a>Verrijkings structuur

Tijdens de uitvoering van de [stappen in een verrijkings pijplijn](cognitive-search-concept-intro.md#enrichment-steps)volgt de verwerking van de *document barsten* wanneer tekst en afbeeldingen worden geëxtraheerd uit de bron. De inhoud van de installatie kopie kan vervolgens worden doorgestuurd naar vaardig heden die de verwerking van afbeeldingen specificeren, terwijl tekst inhoud in de wachtrij staat voor tekst verwerking. Voor bron documenten die grote hoeveel heden tekst bevatten, kunt u een *parseringsfout* op de Indexeer functie instellen om tekst in kleinere segmenten te segmenteren voor een betere verwerking. 

![Kennis archief in pijplijn diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kennis archief in pijplijn diagram")

Zodra een document zich in de verrijkings pijplijn bevindt, wordt het weer gegeven als een structuur van inhoud en bijbehorende verrijkingen. Deze boom structuur wordt geïnstantieerd als de uitvoer van het kraken van documenten.  De indeling van de verrijkings structuur maakt het mogelijk om meta gegevens te koppelen aan zelfs primitieve gegevens typen, maar dit is geen geldig JSON-object, maar kan worden geprojecteerd naar een geldige JSON-indeling. In de volgende tabel ziet u de status van een document dat in de verrijkings pijplijn wordt ingevoerd:

|Modus data Source\Parsing|Standaard|JSON, JSON-lijnen & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N.v.t. |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N.v.t.|

 Wanneer vaardig heden worden uitgevoerd, voegen ze nieuwe knoop punten toe aan de verrijkings structuur. Deze nieuwe knoop punten kunnen vervolgens worden gebruikt als invoer voor downstream-vaardig heden, projecteren naar het kennis archief of toewijzing aan index velden. Verrijkingen zijn niet onveranderbaar: als u deze eenmaal hebt gemaakt, kunnen knoop punten niet worden bewerkt. Naarmate uw vaardig heden ingewik kelder wordt, is uw verrijkings structuur, maar niet alle knoop punten in de verrijkings structuur nodig om deze naar de index of het kennis archief te brengen. 

U kunt slechts een subset van de verrijkingen van de index of het kennis archief selectief blijven.

### <a name="context"></a>Context

Elke vaardigheid vereist een context. Een context bepaalt het volgende:

+ Het aantal keren dat de vaardigheid wordt uitgevoerd, op basis van de geselecteerde knoop punten. Bij context waarden van het type verzameling wordt een `/*` aan het einde toegevoegd, waardoor de vaardigheid één keer wordt aangeroepen voor elke instantie in de verzameling. 

+ In de verrijkings structuur worden de vaardigheids uitvoer toegevoegd. Uitvoer wordt altijd aan de structuur toegevoegd als onderliggende elementen van het context knooppunt. 

+ Vorm van de invoer. Voor verzamelingen op meerdere niveaus is het instellen van de context voor de bovenliggende verzameling van invloed op de vorm van de invoer voor de vaardigheid. Als u bijvoorbeeld een verrijkings structuur hebt met een lijst met landen/regio's, wordt elke verrijkt met een lijst met statussen die een lijst met post codes bevatten.

|Context|Invoer|Vorm van invoer|Aanroepen van vaardig heden|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Een lijst met alle post codes in het land/de regio |Eenmaal per land/regio |
|`/document/countries/*/states/*` |'/document/countries/*/States/*/ZIPcodes/* ' ' |Een lijst met post codes in de status | Eenmaal per combi natie van land/regio en provincie|

## <a name="generate-enriched-data"></a>Verrijkte gegevens genereren 

Met behulp van de weer gave van de [Hotels-beoordelings vaardigheden](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) als referentie punt, kijken we naar:

+ Hoe de verrijkings structuur zich ontwikkelt met de uitvoering van elke vaardigheid
+ De manier waarop de context en invoer bepalen hoe vaak een vaardigheid wordt uitgevoerd
+ De vorm van de invoer op basis van de context

Een ' document ' binnen het verrijkings proces vertegenwoordigt één rij (een hotel beoordeling) binnen het hotel_reviews.csv bron bestand.

### <a name="skill-1-split-skill"></a>Vaardigheids #1: vaardigheid splitsen

Als de bron inhoud bestaat uit grote delen van tekst, is het handig om deze op te splitsen in kleinere onderdelen voor een grotere nauw keurigheid van de detectie van taal, sentiment en sleutel woorden. Er zijn twee korrels beschikbaar: pagina's en zinnen. Een pagina bestaat uit ongeveer 5000 tekens.

Een tekst scheidings kwalificatie is doorgaans het eerst in een vaardighedenset.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

Met de vaardigheids context van `"/document/reviews_text"` wordt de gesplitste vaardigheid één keer uitgevoerd voor de `reviews_text` . De vaardigheids uitvoer is een lijst waarvan de `reviews_text` is gesegmenteerd in 5000 teken segmenten. De uitvoer van de gesplitste vaardigheid heet `pages` en wordt toegevoegd aan de verrijkings structuur. Met deze `targetName` functie kunt u de naam van een vaardigheids uitvoer wijzigen voordat u deze aan de verrijkings structuur toevoegt.

De verrijkings structuur heeft nu een nieuw knoop punt dat is geplaatst in de context van de vaardigheid. Dit knoop punt is beschikbaar voor elke vaardigheid, projectie of uitvoer veld toewijzing. In het algemeen ziet de structuur er als volgt uit:

![verrijkings structuur na het kraken van documenten](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Verrijkings structuur na het kraken van documenten en voordat de kwalificatie wordt uitgevoerd")

Het hoofd knooppunt voor alle verrijkingen is `"/document"` . Bij het werken met Blob-Indexeer functies `"/document"` heeft het knoop punt onderliggende knoop punten van `"/document/content"` en `"/document/normalized_images"` . Als we in dit voor beeld werken met CSV-gegevens, worden de kolom namen toegewezen aan de onderliggende knoop punten `"/document"` . 

Voor toegang tot een van de verrijkingen die aan een knoop punt worden toegevoegd door een vaardigheid, is het volledige pad voor de verrijking nodig. Als u de tekst van het knoop punt bijvoorbeeld wilt gebruiken ```pages``` als invoer voor een andere vaardigheid, moet u deze opgeven als ```"/document/reviews_text/pages/*"``` .
 
 ![verrijkings structuur na vaardigheid #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Verrijkings structuur nadat Kwalificatie #1 uitgevoerd")

### <a name="skill-2-language-detection"></a>Taal detectie van vaardigheids #2

Beoordelings documenten voor hotels bevatten feedback van klanten, uitgedrukt in meerdere talen. De vaardigheid van de taal detectie bepaalt welke taal wordt gebruikt. Het resultaat wordt vervolgens door gegeven aan de sleutel woord extractie en sentiment detectie, waarbij taal wordt overwogen bij het detecteren van sentiment en zinsdelen.

Hoewel de vaardigheid van taal detectie de derde vaardigheid (vaardigheids #3) is gedefinieerd in de vaardig heden, is dit de volgende vaardigheid die moet worden uitgevoerd. Omdat het niet wordt geblokkeerd door invoer te vereisen, wordt deze parallel uitgevoerd met de vorige vaardigheid. Net als de gesplitste vaardigheid die erop wordt toegepast, wordt de vaardigheid van de taal detectie ook één keer voor elk document aangeroepen. De verrijkings structuur heeft nu een nieuw knoop punt voor taal.

 ![verrijkings structuur na vaardigheid #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Verrijkings structuur nadat Kwalificatie #2 uitgevoerd")
 
 ### <a name="skill-3-key-phrases-skill"></a>Vaardig heden #3: vaardigheid van sleutel zinnen 

Gezien de context van `/document/reviews_text/pages/*` de vaardig heden van de sleutel zinnen wordt één keer aangeroepen voor elk van de items in de `pages` verzameling. De uitvoer van de vaardigheid is een knoop punt onder het bijbehorende pagina-element. 

 Nu kunt u de rest van de vaardig heden in de vakkennisset bekijken en visualiseren hoe de structuur van verrijkingen blijft groeien met de uitvoering van elke vaardigheid. Sommige vaardig heden, zoals het samen voegen van vaardig heden en de shaper-vaardigheid, maken ook nieuwe knoop punten, maar gebruiken alleen gegevens van bestaande knoop punten en maken geen nieuwe verrijkingen.

![verrijkings structuur na alle vaardig heden](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Verrijkings structuur na alle vaardig heden")

De kleuren van de connectors in de bovenstaande structuur geven aan dat de verrijkingen zijn gemaakt door verschillende vaardig heden en dat de knoop punten afzonderlijk moeten worden behandeld en geen deel uitmaken van het object dat wordt geretourneerd bij het selecteren van het bovenliggende knoop punt.

## <a name="save-enrichments"></a>Verrijkingen besparen

In azure Cognitive Search slaat een Indexeer functie de uitvoer op die wordt gemaakt. Een van de uitvoer is altijd een [Doorzoek bare index](search-what-is-an-index.md). Het opgeven van een index is een vereiste en wanneer u een vaardig heden koppelt, bevat de gegevens die door een index zijn opgenomen, de economische realiteit van de verrijkingen. Normaal gesp roken worden de uitvoer van specifieke vaardig heden, zoals sleutel zinnen of sentimente scores, opgenomen in de index in een veld dat voor dat doel wordt gemaakt.

Een Indexeer functie kan eventueel ook de uitvoer naar een [kennis archief](knowledge-store-concept-intro.md) verzenden voor gebruik in andere hulpprogram ma's of processen. Een kennis archief wordt gedefinieerd als onderdeel van de vaardig heden. De definitie bepaalt of uw verrijkte documenten worden geprojecteerd als tabellen of objecten (bestanden of blobs). Tabellaire projecties zijn zeer geschikt voor interactieve analyses in hulpprogram ma's zoals Power BI, terwijl bestanden en blobs doorgaans worden gebruikt in gegevens wetenschap of soort gelijke processen. In deze sectie leert u hoe u met de samen stelling van de vaardig heden de tabellen of objecten kunt vorm geven die u wilt projecteren.

### <a name="projections"></a>Projecties

Voor inhoud die gericht is op een Knowledge Store, moet u nagaan hoe de inhoud is gestructureerd. *Projectie* is het proces van het selecteren van de knoop punten van de verrijkings structuur en het maken van een fysieke expressie hiervan in het kennis archief. Projecties zijn aangepaste vormen van het document (inhoud en verrijkingen) dat kan worden uitgevoerd als tabel-of object projecties. Zie [werken met projecties](knowledge-store-projection-overview.md)voor meer informatie over het werken met projecties.

![Opties voor veld toewijzing](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opties voor veld toewijzing voor verrijkings pijplijn")

### <a name="sourcecontext"></a>SourceContext

Het `sourceContext` element wordt alleen gebruikt voor invoer van vaardig heden en projecties. Dit wordt gebruikt om geneste multi-level-objecten te bouwen. Mogelijk moet u een nieuw object maken om het door te geven als invoer voor een vaardigheid of project in het kennis archief. Omdat verrijkings knooppunten mogelijk geen geldig JSON-object in de verrijkings structuur zijn en verwijzen naar een knoop punt in de structuur, wordt alleen die status van het knoop punt geretourneerd toen het werd gemaakt, met behulp van de verrijkingen als vaardig heden of projecties moet u een goed gevormd JSON-object maken. `sourceContext`Met de kunt u een hiërarchisch, anoniem type object maken dat meerdere vaardig heden zou moeten hebben als u de context alleen gebruikt. 

Gebruiken `sourceContext` wordt weer gegeven in de volgende voor beelden. Bekijk de vaardigheids uitvoer die een verrijking heeft gegenereerd om te bepalen of het een geldig JSON-object is en niet een primitief type.

### <a name="slicing-projections"></a>Segmenteer projecties

Bij het definiëren van een tabel projectie groep kan een enkel knoop punt in de verrijkings structuur worden gesegmenteerd in meerdere gerelateerde tabellen. Als u een tabel met een bronpad toevoegt die een onderliggend element van een bestaande tabel projectie is, is het resulterende onderliggende knoop punt geen onderliggend element van de bestaande tabel projectie, maar wordt er in plaats daarvan geprojecteerd in de nieuwe, gerelateerde tabel. Met deze segmenting techniek kunt u één knoop punt in een shaper-vaardigheid definiëren dat de bron voor alle tabel projecties kan zijn. 

### <a name="shaping-projections"></a>Vorm projectie

Er zijn twee manieren om een projectie te definiëren:

+ Gebruik de tekst shaper-vaardigheid om een nieuw knoop punt te maken dat het hoofd knooppunt is voor alle verrijkingen die u projecteren. In uw projecties zou u alleen verwijzen naar de uitvoer van de shaper-vaardigheid.

+ Gebruik een inlinevorm in de projectie definitie zelf.

De shaper-benadering is uitgebreider dan in line vorm, maar zorgt ervoor dat alle mutaties van de verrijkings structuur in de vaardig heden zijn opgenomen en dat de uitvoer een object is dat opnieuw kan worden gebruikt. Met inline-vormgeving kunt u daarentegen de vorm maken die u nodig hebt, maar dit is een anoniem object dat alleen beschikbaar is voor de projectie waarvoor het is gedefinieerd. De benaderingen kunnen samen of afzonderlijk worden gebruikt. De vaardig heden die u voor u hebt gemaakt in de werk stroom van de portal bevat beide. Er wordt gebruikgemaakt van een shaper-vaardigheid voor de tabel projecties, maar maakt ook gebruik van inline-vorm om de tabel met sleutel zinnen te projecteren.

Om het voor beeld uit te breiden, kunt u ervoor kiezen om de inline-vorm te verwijderen en een shaper-vaardigheid te gebruiken om een nieuw knoop punt voor de sleutel zinnen te maken. Als u een vorm wilt maken die in drie tabellen is geprojecteerd, namelijk,, `hotelReviewsDocument` `hotelReviewsPages` en `hotelReviewsKeyPhrases` , worden de twee opties beschreven in de volgende secties.

#### <a name="shaper-skill-and-projection"></a>Shaper vaardig heden en projectie

> [!Note]
> Enkele van de kolommen uit de document tabel zijn uit dit voor beeld verwijderd voor de boog.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Met het `tableprojection` knoop punt dat in de `outputs` bovenstaande sectie is gedefinieerd, kunnen we nu de segmenting-functie gebruiken om delen van het `tableprojection` knoop punt in verschillende tabellen te projecteren:

> [!Note]
> Dit is slechts een fragment van de projectie binnen de configuratie van het kennis archief.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Inline-projectie prognoses

Voor de inline-vorm benadering is geen shaper-vaardigheid vereist, omdat alle shapes die nodig zijn voor de projecties, worden gemaakt op het moment dat ze nodig zijn. Als u dezelfde gegevens wilt projecteren als in het vorige voor beeld, ziet de optie inline-projectie er als volgt uit:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Een waarneming van beide benaderingen is hoe waarden van `"Keyphrases"` worden geprojecteerd met behulp van de `"sourceContext"` . Het `"Keyphrases"` knoop punt, dat een verzameling teken reeksen bevat, is zelf een onderliggend element van de pagina tekst. Omdat projecties echter een JSON-object vereisen en de pagina een primitieve (teken reeks) is, `"sourceContext"` wordt het gebruikt om de sleutel woorden in een object met een benoemde eigenschap op te slaan. Met deze techniek kunnen zelfs primitieven worden geprojecteerd.

## <a name="next-steps"></a>Volgende stappen

Als volgende stap maakt u uw eerste vaardig heden met cognitieve vaardig heden.

> [!div class="nextstepaction"]
> [Maak uw eerste vaardig heden](cognitive-search-defining-skillset.md)-set.
